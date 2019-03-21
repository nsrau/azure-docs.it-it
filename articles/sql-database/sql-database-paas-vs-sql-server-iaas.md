---
title: Scelta tra le opzioni SQL in Azure | Microsoft Docs
description: Informazioni su come scegliere tra le opzioni di distribuzione all'interno del database SQL di Azure e tra il database SQL di Azure e SQL Server per macchine virtuali di Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server cloud, SQL Server nel cloud, database PaaS, cloud SQL Server, DBaaS
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: e5f400bb25fce58a4fb63ca1ad76ddf2b71fbd9c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835419"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Scegliere l'opzione SQL Server più adatta in Azure

In Azure i carichi di lavoro di SQL Server possono essere eseguiti in un'infrastruttura ospitata (IaaS) o come servizio ospitato ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). In PaaS sono disponibili più opzioni di distribuzione e diversi livelli di servizio all'interno di ogni opzione di distribuzione. La domanda fondamentale che è necessario porsi prima di decidere tra PaaS o IaaS è: si vuole gestire il database, applicare le patch, eseguire i backup o si preferisce delegare queste operazioni ad Azure?
A seconda della risposta, sono disponibili le opzioni seguenti:

- [Database SQL di Azure](sql-database-technical-overview.md): Un motore di database SQL completamente gestito, basato sulla versione stabile più recente di SQL Server Enterprise Edition. Si tratta di un database relazionale distribuito come servizio (DBaaS) ospitato nel cloud di Azure, che rientra nella categoria di settore *piattaforma distribuita come servizio (PaaS)*. Il database SQL offre più opzioni di distribuzione, ognuna delle quali si basa su hardware e software standardizzati appartenenti, ospitati e gestiti da Microsoft. Con il database SQL è possibile usare caratteristiche e funzionalità predefinite che richiedono una configurazione complessa se usate in SQL Server, in locale o in una macchina virtuale di Azure. Il database SQL prevede un pagamento in base al consumo con la possibilità di aumentare o diminuire il numero di istanze per una maggiore efficienza e senza interruzioni. Il database SQL offre caratteristiche aggiuntive non disponibili in SQL Server, come le funzionalità di disponibilità elevata, gestione e intelligence incorporate. Il database SQL di Azure offre le opzioni di distribuzione seguenti:
  
  - Come [database singolo](sql-database-single-database.md) con uno specifico set di risorse gestito tramite server di database SQL. Un database singolo è simile a un [database indipendente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) in SQL Server. Questa opzione è ottimizzata per lo sviluppo di nuove applicazioni moderne progettate per il cloud.
  - Un [pool elastico](sql-database-elastic-pool.md), ovvero una raccolta di database con un set condiviso di risorse gestito tramite un server di database SQL. I database singoli possono essere spostati all'interno e all'esterno di un pool elastico. Questa opzione è ottimizzata per lo sviluppo di nuove applicazioni moderne progettate per il cloud tramite l'applicazione SaaS multi-tenant.
  - [Istanza gestita](sql-database-managed-instance.md), ovvero una raccolta di database utente e di sistema con un set condiviso di risorse. Un'istanza gestita è simile a un'istanza del [motore di database Microsoft SQL Server] che offre risorse condivise per i database e funzionalità con ambito di istanza aggiuntive. Istanza gestita supporta la migrazione del database dall'ambiente locale con pochissime o nessuna modifica al database stesso. Questa opzione assicura tutti i vantaggi di PaaS offerti dal database SQL di Azure, aggiungendo funzionalità precedentemente disponibili solo nelle macchine virtuali SQL, quali una rete virtuale (VNet) nativa e una compatibilità quasi del 100% con SQL Server locale.
- [SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) rientra nella categoria di settore *IaaS (Infrastructure-as-a-Service)* e consente di eseguire SQL Server in una macchina virtuale completamente gestita nel cloud di Azure. Anche le [macchine virtuali SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) vengono eseguite su hardware standardizzato appartenente, ospitato e gestito da Microsoft. Quando si usa SQL Server in una macchina virtuale, è possibile scegliere una licenza di SQL Server con pagamento in base al consumo già inclusa in un'immagine di SQL Server oppure una licenza esistente. È anche possibile arrestare o riavviare la macchina virtuale in base alle esigenze. SQL Server installato e ospitato nel cloud in macchine virtuali (VM) Windows Server o Linux in esecuzione in Azure, una configurazione nota anche come infrastruttura distribuita come servizio (IaaS). SQL Server in macchine virtuali di Azure è un'opzione valida per la migrazione di applicazioni e database SQL Server locali senza la necessità di modifiche al database. Tutte le versioni e le edizioni recenti di SQL Server sono disponibili per l'installazione in una macchina virtuale IaaS. La differenza più significativa rispetto al database SQL è che le macchine virtuali di SQL Server assicurano il controllo completo sul motore di database. È possibile scegliere quando verrà avviata la manutenzione/applicazione di patch, modificare il modello di recupero per la registrazione semplice o minima delle operazioni bulk in modo da abilitare il caricamento più rapido con minore registrazione, sospendere o avviare il motore quando necessario ed è anche possibile personalizzare completamente il motore di database di SQL Server. Questo ulteriore controllo implica una maggiore responsabilità per la gestione delle macchine virtuali.

Le differenze principali tra queste opzioni sono elencate nella tabella seguente:

| SQL Server in VM | Istanza gestita nel database SQL | Database singolo/pool elastico nel database SQL |
| --- | --- | --- |
|Pieno controllo sul motore di SQL Server.<br/>Disponibilità fino al 99,95%.<br/>Stesse funzionalità della versione corrispondente di SQL Server in locale.<br/>Versione del motore di database fissa e ben nota.<br/>Facilità di migrazione da SQL Server in locale.<br/>Indirizzo IP privato all'interno della rete virtuale di Azure.<br/>Possibilità di distribuire le applicazioni o i servizi nell'host in cui si trova SQL Server.| Elevata compatibilità con SQL Server in locale.<br/>Disponibilità garantita al 99,99%.<br/>Backup predefiniti, applicazione di patch, ripristino.<br/>Ultima versione stabile del motore di database.<br/>Facilità di migrazione da SQL Server.<br/>Indirizzo IP privato all'interno della rete virtuale di Azure.<br/>Avanzate funzionalità integrate di intelligence e sicurezza.<br/>Modifica online delle risorse (CPU/archiviazione).|Sono disponibili le funzionalità di SQL Server più usate.<br/>Disponibilità garantita al 99,99%.<br/>Backup predefiniti, applicazione di patch, ripristino.<br/>Ultima versione stabile del motore di database.<br/>Possibilità di assegnare le risorse necessarie (CPU/archiviazione) a singoli database.<br/>Avanzate funzionalità integrate di intelligence e sicurezza.<br/>Modifica online delle risorse (CPU/archiviazione).|
|È necessario gestire i backup e le patch.<br>È necessario implementare una soluzione a disponibilità elevata.<br/>Tempo di inattività durante la modifica delle risorse (CPU/archiviazione)|È ancora presente un numero limitato di funzionalità di SQL Server che non sono disponibili.<br/>Nessun tempo di manutenzione garantito (ma quasi trasparente).<br/>La compatibilità con la versione di SQL Server può essere ottenuta solo tramite i livelli di compatibilità del database.|La migrazione da SQL Server può essere complessa.<br/>Alcune funzionalità di SQL Server non sono disponibili.<br/>Nessun tempo di manutenzione garantito (ma quasi trasparente).<br/>La compatibilità con la versione di SQL Server può essere ottenuta solo tramite i livelli di compatibilità del database.<br/>Non è possibile assegnare un indirizzo IP privato (è possibile limitare l'accesso usando le regole del firewall).|

Informazioni sul modo in cui ogni opzione di distribuzione si inserisce nella piattaforma dati Microsoft e su come ottenere assistenza per abbinare l'opzione giusta alle esigenze aziendali. Indipendentemente dai fattori che determinano la scelta, ad esempio i costi o la gestione ridotta, questo articolo può aiutare a decidere l'approccio ottimale rispetto alle esigenze aziendali più importanti.

## <a name="microsofts-sql-data-platform"></a>Piattaforma dati SQL Microsoft

Una delle prime cose da comprendere in una discussione in cui Azure viene confrontato con i database SQL Server locali è che è possibile usare tutte le soluzioni. La piattaforma dei dati Microsoft si basa sulla tecnologia SQL Server e la rende disponibile nei computer fisici locali, negli ambienti cloud privati, negli ambienti cloud privati ospitati da terze parti e nel cloud pubblico. SQL Server in macchine virtuali di Azure permette di soddisfare numerose esigenze aziendali specifiche grazie a una combinazione di distribuzioni locali e ospitate su cloud usando lo stesso set di prodotti server, strumenti di sviluppo ed esperienza in tutti gli ambienti.

   ![Opzioni di SQL Server cloud: SQL Server in IaaS o database SQL SaaS nel cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Come mostrato nel diagramma, ogni offerta può essere caratterizzata dal livello di amministrazione che si ha sull'infrastruttura (asse X) e dal grado di efficienza nei costi raggiunto dal consolidamento e automazione a livello di database (asse Y).

Quando si progetta un'applicazione sono disponibili quattro opzioni di base per l'hosting della parte SQL Server dell'applicazione:

- SQL Server in computer fisici non virtualizzati
- SQL Server in macchine virtualizzate locali (cloud privato)
- SQL Server in una macchina virtuale di Azure (cloud Microsoft pubblico)
- Database SQL di Azure (cloud Microsoft pubblico)

Le sezioni seguenti contengono informazioni su SQL Server nel cloud Microsoft pubblico: database SQL di Azure e SQL Server in macchine virtuali di Azure. L'articolo illustra anche i vantaggi aziendali più diffusi che permettono di determinare l'opzione ottimale per l'applicazione.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Informazioni dettagliate sul database SQL di Azure e su SQL Server in macchine virtuali di Azure

In generale, queste due opzioni SQL sono ottimizzate per scopi diversi:

- **Database SQL di Azure**

È ottimizzato per ridurre al minimo i costi di gestione complessivi per il provisioning e la gestione di numerosi database. Riduce i costi amministrativi correnti perché non è necessario gestire le macchine virtuali, il sistema operativo o il software del database, E non è neanche necessario gestire gli aggiornamenti, la disponibilità elevata o i [backup](sql-database-automated-backups.md). In generale, il database SQL di Azure può aumentare significativamente il numero di database gestiti da una singola risorsa IT o di sviluppo. I [pool elastici](sql-database-elastic-pool.md) supportano inoltre le architetture di applicazioni multi-tenant SaaS, con funzionalità che includono l'isolamento dei tenant e la possibilità di ridimensionamento per ridurre i costi condividendo le risorse tra i database. Un'[istanza gestita](sql-database-managed-instance.md) offre supporto per funzionalità con ambito istanza consentendo una migrazione semplificata delle applicazioni esistenti, nonché la condivisione delle risorse tra i database.

- **SQL Server in esecuzione in macchine virtuali di Azure**

È ottimizzato per eseguire la migrazione di applicazioni esistenti in Azure o per estendere nel cloud le applicazioni locali esistenti in distribuzioni ibride. SQL Server in esecuzione in una macchina virtuale può essere usato anche per sviluppare e testare applicazioni SQL Server tradizionali. SQL Server nelle VM di Azure fornisce diritti amministrativi completi per un'istanza di SQL Server dedicata e una macchina virtuale basata sul cloud. È la scelta ideale quando un'organizzazione ha già delle risorse IT disponibili per mantenere le macchine virtuali. Queste funzionalità permettono di creare un sistema altamente personalizzato per soddisfare i requisiti specifici dell'applicazione in termini di prestazioni e disponibilità.

La tabella seguente riepiloga le caratteristiche principali del database SQL di Azure e di SQL Server nelle macchine virtuali di Azure:

| | Database singoli e pool elastici di database SQL | Istanze gestite di database SQL |Macchine virtuali di Azure con SQL Server |
| --- | --- | --- |---|
| **Ideale per:** |Nuove applicazioni progettate per il cloud che vogliono usare le funzionalità stabili di SQL Server più recenti e hanno vincoli di tempo per lo sviluppo e il marketing. | Nuove applicazioni o applicazioni locali esistenti che vogliono usare le funzionalità stabili di SQL Server più recenti e di cui viene eseguita la migrazione al cloud con modifiche minime.  | Applicazioni esistenti che richiedono una migrazione rapida al cloud con modifiche minime o senza modifiche. Scenari di sviluppo e test rapidi quando non si vuole acquistare hardware SQL Server locale non di produzione. |
|  | Team che richiedono funzionalità incorporate di disponibilità elevata, ripristino di emergenza e aggiornamento per il database. | Uguali ai database SQL singoli e in pool. | Team che possono configurare, ottimizzare, personalizzare e gestire la disponibilità elevata, il ripristino di emergenza e l'applicazione di patch per SQL Server. Alcune funzionalità automatiche fornite semplifica notevolmente queste operazioni. |
|  | Team che preferiscono non gestire il le impostazioni del sistema operativo e di configurazione sottostanti. | Uguali ai database SQL singoli e in pool. | Casi in cui è necessario un ambiente personalizzato con diritti amministrativi completi. |
|  | Database fino a 100 TB. | Fino a 8 TB. | Istanze di SQL Server con un massimo di 64 TB di spazio di archiviazione. L'istanza può supportare tutti i database necessari. |
| **Compatibilità** | Supporta la maggior parte delle funzionalità a livello di database locali. | Supporta quasi tutte le funzionalità a livello di database e a livello di istanza locali. | Supporta tutte le funzionalità locali. |
| **Risorse:** | Non si vogliono usare risorse IT per la configurazione e la gestione dell'infrastruttura sottostante, ma ci si vuole concentrare sul livello dell'applicazione. | Uguali ai database SQL singoli e in pool. | Sono disponibili alcune risorse IT per la configurazione e la gestione. Alcune funzionalità automatiche fornite semplifica notevolmente queste operazioni. |
| **Costo totale di proprietà:** | Elimina i costi associati all'hardware e riduce i costi amministrativi. | Uguali ai database SQL singoli e in pool. | Elimina i costi associati all'hardware. |
| **Continuità aziendale:** |Oltre alle [funzionalità di tolleranza di errore predefinite dell'infrastruttura](sql-database-high-availability.md), il database SQL di Azure offre funzionalità come [backup automatici](sql-database-automated-backups.md), [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore), [ripristino geografico](sql-database-recovery-using-backups.md#geo-restore), [replica geografica attiva](sql-database-active-geo-replication.md) e [gruppi di failover automatico](sql-database-auto-failover-group.md) per incrementare la continuità aziendale. Per altre informazioni, vedere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](sql-database-business-continuity.md). | Uguali ai database SQL singoli e in pool, in più sono disponibili backup di sola copia avviati dall'utente. | SQL Server in macchine virtuali di Azure consente di configurare una soluzione con disponibilità elevata e ripristino di emergenza per le esigenze specifiche del database. È quindi possibile avere un sistema altamente ottimizzato per la propria applicazione. È possibile testare ed eseguire i failover autonomamente quando necessario. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Cloud ibrido:** |L'applicazione locale può accedere ai dati nel database SQL di Azure. | [Implementazione della rete virtuale nativa](sql-database-managed-instance-vnet-configuration.md) e connettività all'ambiente locale mediante Azure Express Route o Gateway VPN. | Con SQL Server nelle macchine virtuali di Azure è possibile eseguire le applicazioni in parte nel cloud e in parte in locale. Ad esempio, è possibile estendere la rete locale e un dominio di Active Directory nel cloud tramite la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per altre informazioni sulle soluzioni di cloud ibrido, vedere [Estensione di soluzioni dati locali nel cloud](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Supporta la [replica transazionale di SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) come sottoscrittore per la replica dei dati. | Per l'istanza gestita la replica è supportata come funzionalità di anteprima. | Supporta pienamente la [replica transazionale di SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Gruppi di disponibilità AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services e il log shipping per la replica dei dati. Supporta pienamente anche i backup di SQL Server tradizionali. |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivazioni aziendali alla base della scelta del database SQL di Azure o di SQL Server nelle macchine virtuali di Azure

Esistono molti fattori che possono influenzare la decisione di scegliere PaaS o IaaS per ospitare i database SQL:

- [Costo](#cost): entrambe le opzioni PaaS e IaaS includono un prezzo base che copre l'infrastruttura e le licenze sottostanti. Tuttavia, con l'opzione IaaS è necessario investire tempo e risorse aggiuntivi per la gestione del database, mentre nelle soluzioni PaaS queste funzionalità di amministrazione sono incluse nel prezzo. L'opzione IaaS consente di arrestare le risorse quando non vengono usate per ridurre i costi, mentre la versione PaaS è sempre in esecuzione, a meno che le risorse non vengano eliminate e ricreate quando sono necessarie.
- [Amministrazione](#administration): le opzioni PaaS riducono la quantità di tempo da investire per amministrare il database. Tuttavia, limita anche la gamma di attività di amministrazione personalizzate e gli script che è possibile eseguire o eseguire. Ad esempio, CLR non è supportato con database singolo o in pool, ma è supportato per un'istanza gestita. Inoltre, nessuna opzione di distribuzione nelle soluzioni PaaS supportano l'utilizzo dei flag di traccia.
- [Contratto di servizio](#service-level-agreement-sla): sia PaaS che IaaS forniscono un contratto di servizio elevato e standard del settore. L'opzione PaaS garantisce un contratto di servizio del 99,99%, mentre l'opzione IaaS garantisce un contratto di servizio del 99,95% per l'infrastruttura, ovvero è necessario implementare meccanismi aggiuntivi per garantire la disponibilità dei database. Nel caso estremo, se si vuole implementare una soluzione a disponibilità elevata che corrisponda a PaaS, è necessario creare sistemi SQL Server aggiuntivi nelle macchine virtuali e configurare gruppi di disponibilità AlwaysOn, che potrebbero raddoppiare il costo del database.
- [Tempo per il trasferimento ad Azure](#market): SQL Server in Macchine virtuali di Azure corrisponde esattamente all'ambiente del cliente, pertanto la migrazione dall'ambiente locale alla macchina virtuale SQL di Azure non è diversa dallo spostamento dei database da un server locale a un altro. Un'istanza gestita consente anche la migrazione in modo molto semplice. Prima della migrazione a un'istanza gestita, tuttavia, può essere necessario apportare alcune modifiche.

Questi fattori verranno illustrati più in dettaglio nelle sezioni seguenti.

### <a name="cost"></a>Costi

Una startup con pochi fondi o un team di un'azienda consolidata con vincoli di budget: due esempi in cui la limitata disponibilità economica rappresenta un fattore primario nella scelta dell'hosting dei database. Questa sezione fornisce le nozioni di base relative a fatturazione e licenze in Azure per le due opzioni di database relazionali: database SQL di Azure e SQL Server in macchine virtuali di Azure. oltre a informazioni su come calcolare il costo totale dell'applicazione.

#### <a name="billing-and-licensing-basics"></a>Nozioni di base su fatturazione e licenze

Il **database SQL**, attualmente venduto come servizio, è disponibile con svariate opzioni di distribuzione e con livelli di servizio diversi, con prezzi diversi per le risorse, tutte fatturate su base oraria a una tariffa fissa, a seconda del livello di servizio e delle dimensioni di calcolo scelti. Per le informazioni più aggiornate sui livelli di servizio, sulle dimensioni di calcolo e sulle quantità di risorse di archiviazione attualmente supportati, vedere il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) e il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

- Con un singolo database SQL è possibile scegliere un livello di servizio adatto alle proprie esigenze in una vasta gamma di prezzi, a partire da 5 dollari al mese per il livello Basic.
- È possibile creare [pool elastici](sql-database-elastic-pool.md) per condividere le risorse tra le istanze del database, ridurre i costi e far fronte ai picchi di utilizzo.
- Con un'istanza gestita del database SQL è anche possibile scegliere l'opzione Bring Your Own License (BYOL). Per altre informazioni sulla funzionalità Bring Your Own License, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/) o usare il [calcolatore del Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) per scoprire come **risparmiare fino al 40%**.

Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale. È possibile modificare in modo dinamico i livelli di servizio e le dimensioni di calcolo per soddisfare le diverse esigenze di velocità effettiva dell'applicazione.

Con il **database SQL**, Microsoft configura automaticamente il software del database, applica le patch ed esegue l'aggiornamento, riducendo quindi i costi amministrativi. Le funzionalità di [backup predefinite](sql-database-automated-backups.md) consentono anche di ottenere una significativa riduzione dei costi, specialmente per un numero elevato di database.

Con **SQL Server in macchine virtuali di Azure**è possibile usare un'immagine di SQL Server fornita dalla piattaforma, che include una licenza, oppure la propria licenza di SQL Server. Sono disponibili tutte le versioni (2008 R2, 2012, 2014, 2016) e le edizioni (Developer, Express, Web, Standard, Enterprise) supportate di SQL Server. Sono anche disponibili le versioni BYOL (Bring Your Own License) delle immagini. Quando si usano le immagini fornite da Azure, il costo operativo dipende dalle dimensioni della macchina virtuale e dalla versione di SQL Server scelta. Indipendentemente dalle dimensioni della macchina virtuale o dall'edizione di SQL Server, si paga il costo al minuto della licenza di SQL Server e Windows o Linux Server, insieme al costo di Archiviazione di Azure per i dischi delle VM. L'opzione di fatturazione al minuto consente di usare SQL Server per tutto il tempo necessario, senza dover acquistare licenze di SQL Server aggiuntive. Se si usa la funzionalità Bring Your Own License per SQL Server in Azure, vengono addebitati solo i costi per il server e l'archiviazione. Per altre informazioni sulla funzionalità Bring Your Own License, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale.

#### <a name="calculating-the-total-application-cost"></a>Calcolo del costo totale dell'applicazione

Quando si inizia a usare una piattaforma cloud, il costo per l'esecuzione dell'applicazione include il costo per il nuovo sviluppo e i costi amministrativi correnti, oltre ai costi del servizio della piattaforma cloud pubblica.

**Quando si usa il database SQL di Azure:**

- Costi di amministrazione estremamente ridotti
- Costi di sviluppo limitati per le applicazioni sottoposte a migrazione (istanze gestite)
- Costi di servizio del database SQL
- Nessun costo per l'acquisto di hardware

**Quando si usa SQL Server nelle macchine virtuali di Azure:**

- Costi di amministrazione più elevati
- Costi di sviluppo limitati o assenti per le applicazioni sottoposte a migrazione
- Costi di servizio delle macchine virtuali
- Nessun costo per l'acquisto di hardware

Per altre informazioni sui prezzi, vedere le seguenti risorse:

- [Prezzi di Database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) per [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

Per molte aziende, la decisione di passare a un servizio cloud riguarda sia la possibilità di ridurre la complessità dell'amministrazione sia i costi. Con IaaS e PaaS, Microsoft amministra l'infrastruttura sottostante e replica automaticamente tutti i dati per fornire il ripristino di emergenza, configura e aggiorna il software del database, gestisce il bilanciamento del carico ed esegue un failover trasparente in caso di errore del server all'interno di un data center.

- Con il **database SQL di Azure**, è possibile continuare ad amministrare il database, ma non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Gli esempi di elementi che è possibile continuare a gestire includono i database e gli account di accesso, l'ottimizzazione dell'indice e delle query, nonché il controllo e la sicurezza. Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione e un'amministrazione minime.
- Con **SQL Server in macchine virtuali di Azure**si ha il pieno controllo del sistema operativo e della configurazione dell'istanza di SQL Server. Con una macchina virtuale l'utente può decidere quando aggiornare il sistema operativo e il software del database e quando installare eventuale software aggiuntivo, come l'antivirus. Sono disponibili alcune funzionalità automatizzate che semplificano notevolmente la disponibilità elevata, il backup e l'applicazione di patch. Inoltre, è possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Azure consente di modificare le dimensioni di una macchina virtuale in base alle esigenze. Per informazioni, vedere [Dimensioni delle macchine virtuali e del servizio cloud per Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Contratto di servizio (SLA)

Per molti reparti IT rispettare gli obblighi relativi al tempo di attività di un contratto di servizio è della massima priorità. Questa sezione spiega le condizioni applicate dal contratto di servizio per ogni opzione di hosting del database.

Per il **database SQL**, Microsoft offre un contratto di servizio per la disponibilità del 99,99%. Per le informazioni più recenti, vedere [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/sql-database/).

Per **SQL Server in esecuzione nelle macchine virtuali di Azure**Microsoft fornisce un contratto di servizio con disponibilità del 99,95% che copre solo alla macchina virtuale. Questo contratto di servizio non comprende i processi, ad esempio SQL Server, in esecuzione nella macchina virtuale e richiede che siano ospitate almeno due istanze di VM in un set di disponibilità. Per le informazioni più recenti, vedere il [Contratto di servizio per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Per la disponibilità elevata del database all'interno delle macchine virtuali, è necessario configurare una delle opzioni di disponibilità elevata supportate in SQL Server, ad esempio [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). L'uso di un'opzione di disponibilità elevata supportata non fornisce un contratto di servizio aggiuntivo, ma permette di ottenere una disponibilità del database superiore al 99,99%.

### <a name="market"></a>È il momento di passare ad Azure

I**pool elastici o database singoli di database SQL** sono la soluzione ideale per le applicazioni progettate per il cloud quando i fattori critici sono la produttività degli sviluppatori e un time-to-market rapido per le nuove soluzioni. Con la funzionalità di tipo DBA programmatica, è lo strumento ideale per gli architetti e gli sviluppatori cloud perché riduce la gestione del sistema operativo e del database sottostanti.

L'**istanza gestita di database SQL** semplifica notevolmente la migrazione delle applicazioni esistenti al database SQL di Azure, consentendo di immettere sul mercato in Azure in tempi brevi applicazioni di database sottoposte a migrazione.

**SQL Server in esecuzione in macchine virtuali di Azure** è la scelta ideale se le applicazioni nuove o esistenti richiedono database di grandi dimensioni o l'accesso a tutte le funzionalità in SQL Server o in Windows/Linux e si vogliono evitare i tempi e i costi per l'acquisto di nuovo hardware locale. È anche una soluzione appropriata quando si vuole eseguire la migrazione delle applicazioni e dei database locali esistenti in Azure così come sono, nei casi in cui l'istanza gestita di database SQL di Azure non sia una scelta indicata. Poiché non è necessario modificare la presentazione, l'applicazione e i livelli dati, si risparmiano il tempo e i costi legati a una nuova progettazione dell'architettura della soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sulle ottimizzazioni delle prestazioni eventualmente richieste dalla piattaforma Azure. Per altre informazioni, vedere [Procedure consigliate per le prestazioni di SQL Server nelle macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione asi database SQL, vedere [Il primo database SQL di Azure](sql-database-single-database-get-started.md).
- Vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Per un'introduzione a SQL Server nelle macchine virtuali di Azure, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) .
