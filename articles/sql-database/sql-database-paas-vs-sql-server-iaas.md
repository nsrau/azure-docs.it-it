---
title: Confronto tra il database SQL (PaaS) e SQL Server nel cloud in VM (IaaS) | Documentazione Microsoft
description: Informazioni su come trovare l'opzione di SQL Server cloud ottimale per l'applicazione, ovvero database SQL di Azure (PaaS) o SQL Server nel cloud su macchine virtuali di Azure.
services: sql-database, virtual-machines
keywords: SQL Server cloud, SQL Server nel cloud, database PaaS, cloud SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 0b1eda89d2baaa54b17d8a7c73a2e9c987f0f8eb
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665986"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)

In Azure i carichi di lavoro di SQL Server possono essere eseguiti in un'infrastruttura ospitata (IaaS) o come servizio ospitato ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)):

- [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/): motore di database SQL, basato su SQL Server Enterprise Edition e ottimizzato per lo sviluppo di applicazioni moderne. Per il database SQL di Azure sono disponibili diverse opzioni di distribuzione:
  - È possibile distribuire un database singolo a un [server logico](sql-database-logical-servers.md).
  - È possibile eseguire la distribuzione in un [pool elastico](sql-database-elastic-pool.md) in un [server logico](sql-database-logical-servers.md) per condividere risorse e ridurre i costi. 

      > [!NOTE]
      > Un database SQL di Azure contenente database singoli e in pool offre la maggior parte delle funzionalità di SQL Server in ambito database.

      La figura seguente illustra queste opzioni di distribuzione:

      ![Opzioni di distribuzione](./media/sql-database-technical-overview/deployment-options.png) 
  - È possibile eseguire la distribuzione in un'[Istanza gestita di database SQL di Azure (anteprima)](sql-database-managed-instance.md). 

      > [!NOTE]
      > Con entrambe le versioni, il database SQL di Azure offre ulteriori funzionalità non disponibili in SQL Server, come la gestione e l'intelligenza incorporata. Con la prima versione, con Istanza gestita di database SQL di Azure, il database SQL di Azure offre risorse condivise per i database e altre funzionalità in ambito istanza. Istanza gestita di database SQL di Azure supporta la migrazione del database con pochissime o nessuna modifica al database stesso.
- [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installato e ospitato nel cloud in macchine virtuali (VM) Windows Server o Linux in esecuzione in Azure, una configurazione nota anche come infrastruttura distribuita come servizio (IaaS). SQL Server in macchine virtuali di Azure è un'opzione valida per la migrazione di applicazioni e database SQL Server locali senza la necessità di modifiche al database. Tutte le versioni e le edizioni recenti di SQL Server sono disponibili per l'installazione in una macchina virtuale IaaS. La differenza più significativa rispetto al database SQL è che le macchine virtuali di SQL Server assicurano il controllo completo sul motore di database. È possibile scegliere quando verrà avviata la manutenzione/applicazione di patch, modificare il modello di recupero per la registrazione semplice o minima delle operazioni bulk in modo da abilitare il caricamento più rapido con minore registrazione, sospendere o avviare il motore quando necessario ed è anche possibile personalizzare completamente il motore di database di SQL Server. Questo ulteriore controllo implica una maggiore responsabilità per la gestione delle macchine virtuali.

Informazioni sul modo in cui ogni opzione di distribuzione si inserisce nella piattaforma dati Microsoft e su come ottenere assistenza per abbinare l'opzione giusta alle esigenze aziendali. Indipendentemente dai fattori che determinano la scelta, ad esempio i costi o la gestione ridotta, questo articolo può aiutare a decidere l'approccio ottimale rispetto alle esigenze aziendali più importanti.

## <a name="microsofts-sql-data-platform"></a>Piattaforma dati SQL Microsoft

Una delle prime cose da comprendere in una discussione in cui Azure viene confrontato con i database SQL Server locali è che è possibile usare tutte le soluzioni. La piattaforma dei dati Microsoft si basa sulla tecnologia SQL Server e la rende disponibile nei computer fisici locali, negli ambienti cloud privati, negli ambienti cloud privati ospitati da terze parti e nel cloud pubblico. SQL Server in macchine virtuali di Azure permette di soddisfare numerose esigenze aziendali specifiche grazie a una combinazione di distribuzioni locali e ospitate su cloud usando lo stesso set di prodotti server, strumenti di sviluppo ed esperienza in tutti gli ambienti.

   ![Opzioni di SQL Server cloud: SQL Server su IaaS o database SQL SaaS sul cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Come mostrato nel diagramma, ogni offerta può essere caratterizzata dal livello di amministrazione che si ha sull'infrastruttura (asse X) e dal grado di efficienza nei costi raggiunto dal consolidamento e automazione a livello di database (asse Y).

Quando si progetta un'applicazione sono disponibili quattro opzioni di base per l'hosting della parte SQL Server dell'applicazione:

* SQL Server in computer fisici non virtualizzati
* SQL Server in macchine virtualizzate locali (cloud privato)
* SQL Server in una macchina virtuale di Azure (cloud Microsoft pubblico)
* Database SQL di Azure (cloud Microsoft pubblico)

Le sezioni seguenti contengono informazioni su SQL Server nel cloud Microsoft pubblico, ovvero sul database SQL di Azure e SQL Server in macchine virtuali di Azure. L'articolo illustra anche i vantaggi aziendali più diffusi che permettono di determinare l'opzione ottimale per l'applicazione.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Informazioni dettagliate sul database SQL di Azure e su SQL Server in macchine virtuali di Azure

Il **database SQL di Azure** è un database relazionale distribuito come servizio (DBaaS) ospitato nel cloud di Azure, che rientra nella categoria di settore *piattaforma distribuita come servizio (PaaS)*. [database SQL](sql-database-technical-overview.md) si basa su hardware e software standardizzati appartenenti, ospitati e gestiti da Microsoft. Con il database SQL, è possibile usare funzioni e funzionalità predefinite che richiedono una configurazione complessa in SQL Server. Il database SQL prevede un pagamento in base al consumo con la possibilità di aumentare o diminuire il numero di istanze per una maggiore efficienza e senza interruzioni. Il database SQL di Azure è un ambiente ideale per lo sviluppo di nuove applicazioni nel cloud. Inoltre, con [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md), è possibile scegliere l'opzione Bring Your Own License (BYOL). Questa opzione assicura tutti i vantaggi di PaaS offerti dal database SQL di Azure, aggiungendo funzionalità precedentemente disponibili solo nelle macchine virtuali SQL, quali una rete virtuale (VNet) nativa e una compatibilità quasi del 100% con SQL Server locale. [Istanza gestita](sql-database-managed-instance.md) è ideale per le migrazioni del database locale in Azure con modifiche minime richieste. 

**SQL Server nelle macchine virtuali di Azure (VM)** rientra nella categoria di settore *IaaS (Infrastructure-as-a-Service)* e consente di eseguire SQL Server in una macchina virtuale nel cloud. Anche le [macchine virtuali SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) vengono eseguite su hardware standardizzato appartenente, ospitato e gestito da Microsoft. Quando si usa SQL Server in una macchina virtuale, è possibile scegliere una licenza di SQL Server con pagamento in base al consumo già inclusa in un'immagine di SQL Server oppure una licenza esistente. È anche possibile arrestare o riavviare la macchina virtuale in base alle esigenze.

In generale, queste due opzioni SQL sono ottimizzate per scopi diversi:

* Il **database SQL di Azure** è ottimizzato per ridurre al minimo i costi di gestione complessivi per il provisioning e la gestione di numerosi database. Riduce i costi amministrativi correnti perché non è necessario gestire le macchine virtuali, il sistema operativo o il software del database, E non è neanche necessario gestire gli aggiornamenti, la disponibilità elevata o i [backup](sql-database-automated-backups.md). In generale, il database SQL di Azure può aumentare significativamente il numero di database gestiti da una singola risorsa IT o di sviluppo. I [pool elastici](sql-database-elastic-pool.md) supportano inoltre le architetture di applicazioni multi-tenant SaaS, con funzionalità che includono l'isolamento dei tenant e la possibilità di ridimensionamento per ridurre i costi condividendo le risorse tra i database. [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md) fornisce supporto per funzionalità in ambito istanza consentendo una migrazione semplificata delle applicazioni esistenti, nonché la condivisione delle risorse tra i database.
* **SQL Server in macchine virtuali di Azure** è ottimizzato per eseguire la migrazione di applicazioni esistenti in Azure o per estendere nel cloud le applicazioni locali esistenti in distribuzioni ibride. SQL Server in esecuzione in una macchina virtuale può essere usato anche per sviluppare e testare applicazioni SQL Server tradizionali. SQL Server nelle VM di Azure fornisce diritti amministrativi completi per un'istanza di SQL Server dedicata e una macchina virtuale basata sul cloud. È la scelta ideale quando un'organizzazione ha già delle risorse IT disponibili per mantenere le macchine virtuali. Queste funzionalità permettono di creare un sistema altamente personalizzato per soddisfare i requisiti specifici dell'applicazione in termini di prestazioni e disponibilità.

La tabella seguente riepiloga le caratteristiche principali del database SQL di Azure e di SQL Server nelle macchine virtuali di Azure:

| | database SQL di Azure<br>Server logici, pool elastici e database singoli | database SQL di Azure<br>Istanza gestita |Macchina virtuale di Azure<br>SQL Server |
| --- | --- | --- |---|
| **Ideale per:** |Nuove applicazioni progettate per il cloud che vogliono usare le funzionalità stabili di SQL Server più recenti e hanno vincoli di tempo per lo sviluppo e il marketing. | Nuove applicazioni o applicazioni locali esistenti che vogliono usare le funzionalità stabili di SQL Server più recenti e di cui viene eseguita la migrazione al cloud con modifiche minime.  | Applicazioni esistenti che richiedono una migrazione rapida al cloud con modifiche minime o senza modifiche. Scenari di sviluppo e test rapidi quando non si vuole acquistare hardware SQL Server locale non di produzione. |
|  | Team che richiedono funzionalità incorporate di disponibilità elevata, ripristino di emergenza e aggiornamento per il database. | Uguale al database SQL. | Team che possono configurare, ottimizzare, personalizzare e gestire la disponibilità elevata, il ripristino di emergenza e l'applicazione di patch per SQL Server. Alcune funzionalità automatiche fornite semplifica notevolmente queste operazioni. | |
|  | Team che preferiscono non gestire il le impostazioni del sistema operativo e di configurazione sottostanti. | Uguale al database SQL. | Casi in cui è necessario un ambiente personalizzato con diritti amministrativi completi. | |
|  | Database con dimensioni fino a 4 TB o database di dimensioni superiori che possono essere [partizionati orizzontalmente o verticalmente](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) in base a un modello di scalabilità orizzontale. | Uguale al database SQL. | Istanze di SQL Server con un massimo di 64 TB di spazio di archiviazione. L'istanza può supportare tutti i database necessari. |
| **Compatibilità** | Supporta la maggior parte delle funzionalità a livello di database locali. | Supporta quasi tutte le funzionalità a livello di database e a livello di istanza locali. | Supporta tutte le funzionalità locali. |
| **Risorse:** | Non si vogliono usare risorse IT per la configurazione e la gestione dell'infrastruttura sottostante, ma ci si vuole concentrare sul livello dell'applicazione. | Uguale al database SQL. | Sono disponibili alcune risorse IT per la configurazione e la gestione. Alcune funzionalità automatiche fornite semplifica notevolmente queste operazioni. |
| **Costo totale di proprietà:** | Elimina i costi associati all'hardware e riduce i costi amministrativi. | Uguale al database SQL. | Elimina i costi associati all'hardware. |
| **Continuità aziendale:** |Oltre alle [funzionalità di tolleranza di errore predefinite dell'infrastruttura](sql-database-high-availability.md), il database SQL di Azure offre funzionalità come [backup automatici](sql-database-automated-backups.md), [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore), [ripristino geografico](sql-database-recovery-using-backups.md#geo-restore) e [gruppi di failover e replica geografica attiva](sql-database-geo-replication-overview.md) per incrementare la continuità aziendale. Per altre informazioni, vedere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](sql-database-business-continuity.md). | Uguale al database SQL, oltre a backup di sola copia avviati dall'utente. | SQL Server in macchine virtuali di Azure consente di configurare una soluzione con disponibilità elevata e ripristino di emergenza per le esigenze specifiche del database. È quindi possibile avere un sistema altamente ottimizzato per la propria applicazione. È possibile testare ed eseguire i failover autonomamente quando necessario. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Cloud ibrido:** |L'applicazione locale può accedere ai dati nel database SQL di Azure. | [Implementazione della rete virtuale nativa](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-vnet-configuration) e connettività all'ambiente locale mediante Azure Express Route o Gateway VPN. | Con SQL Server nelle macchine virtuali di Azure è possibile eseguire le applicazioni in parte nel cloud e in parte in locale. Ad esempio, è possibile estendere la rete locale e un dominio di Active Directory nel cloud tramite la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). È anche possibile archiviare i file di dati locali nell'archiviazione di Azure usando [File di dati di SQL Server in Azure](http://msdn.microsoft.com/library/dn385720.aspx). Per altre informazioni, vedere [Introduzione al cloud ibrido di SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Supporta la [replica transazionale di SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) come sottoscrittore per la replica dei dati. | La replica non è supportata per Istanza gestita di database SQL di Azure. | Supporta pienamente la [replica transazionale di SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Gruppi di disponibilità AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services e il log shipping per la replica dei dati. Supporta pienamente anche i backup di SQL Server tradizionali. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivazioni aziendali alla base della scelta del database SQL di Azure o di SQL Server nelle macchine virtuali di Azure
### <a name="cost"></a>Costi
Una startup con pochi fondi o un team di un'azienda consolidata con vincoli di budget: due esempi in cui la limitata disponibilità economica rappresenta un fattore primario nella scelta dell'hosting dei database. Questa sezione fornisce le nozioni di base relative a fatturazione e licenze in Azure per le due opzioni di database relazionali: database SQL di Azure e SQL Server in macchine virtuali di Azure, oltre a informazioni su come calcolare il costo totale dell'applicazione.

#### <a name="billing-and-licensing-basics"></a>Nozioni di base su fatturazione e licenze

Attualmente il **database SQL** viene venduto come servizio ed è disponibile in livelli di servizio differenti con prezzi diversi per le risorse, la cui fatturazione viene applicata su base oraria a una tariffa fissa, a seconda dei livelli di servizio e delle prestazioni scelti. Con Istanza gestita di database SQL di Azure, è anche possibile scegliere l'opzione Bring Your Own License (BYOL). Per altre informazioni sulla funzionalità Bring Your Own License, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale. È possibile modificare in modo dinamico i livelli di servizio e delle prestazioni per soddisfare le diverse esigenze di velocità effettiva dell'applicazione. Per le informazioni più aggiornate sui livelli di servizio attualmente supportati, vedere il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) e il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md). È anche possibile creare [pool elastici](sql-database-elastic-pool.md) per condividere le risorse tra le istanze del database per ridurre i costi e far fronte ai picchi di utilizzo.

Con il **database SQL**, Microsoft configura automaticamente il software del database, applica le patch ed esegue l'aggiornamento, riducendo quindi i costi amministrativi. Le funzionalità di [backup predefinite](sql-database-automated-backups.md) consentono anche di ottenere una significativa riduzione dei costi, specialmente per un numero elevato di database. 

Con **SQL Server in macchine virtuali di Azure**è possibile usare un'immagine di SQL Server fornita dalla piattaforma, che include una licenza, oppure la propria licenza di SQL Server. Sono disponibili tutte le versioni (2008 R2, 2012, 2014, 2016) e le edizioni (Developer, Express, Web, Standard, Enterprise) supportate di SQL Server. Sono anche disponibili le versioni BYOL (Bring Your Own License) delle immagini. Quando si usano le immagini fornite da Azure, il costo operativo dipende dalle dimensioni della macchina virtuale e dalla versione di SQL Server scelta. Indipendentemente dalle dimensioni della macchina virtuale o dall'edizione di SQL Server, si paga il costo al minuto della licenza di SQL Server e Windows o Linux Server, insieme al costo di Archiviazione di Azure per i dischi delle VM. L'opzione di fatturazione al minuto consente di usare SQL Server per tutto il tempo necessario, senza dover acquistare licenze di SQL Server aggiuntive. Se si usa la funzionalità Bring Your Own License per SQL Server in Azure, vengono addebitati solo i costi per il server e l'archiviazione. Per altre informazioni sulla funzionalità Bring Your Own License, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale.

#### <a name="calculating-the-total-application-cost"></a>Calcolo del costo totale dell'applicazione
Quando si inizia a usare una piattaforma cloud, il costo per l'esecuzione dell'applicazione include il costo per il nuovo sviluppo e i costi amministrativi correnti, oltre ai costi del servizio della piattaforma cloud pubblica.

**Quando si usa il database SQL di Azure:**

- Costi di amministrazione estremamente ridotti
- Costi di sviluppo limitati per le applicazioni sottoposte a migrazione
- Costi di servizio del database SQL
- Nessun costo per l'acquisto di hardware

**Quando si usa SQL Server nelle macchine virtuali di Azure:**

- Costi di amministrazione più elevati
- Costi di sviluppo limitati o assenti per le applicazioni sottoposte a migrazione
- Costi di servizio delle macchine virtuali
- Costi di licenza di SQL Server
- Nessun costo per l'acquisto di hardware

Per altre informazioni sui prezzi, vedere le seguenti risorse:

* [Database SQL - Prezzi](https://azure.microsoft.com/pricing/details/sql-database/)
* [Prezzi per le macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) per [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration
Per molte aziende, la decisione di passare a un servizio cloud riguarda sia la possibilità di ridurre la complessità dell'amministrazione sia i costi. Con IaaS e PaaS, Microsoft amministra l'infrastruttura sottostante e replica automaticamente tutti i dati per fornire il ripristino di emergenza, configura e aggiorna il software del database, gestisce il bilanciamento del carico ed esegue un failover trasparente in caso di errore del server all'interno di un data center. 

- Con il **database SQL di Azure**, è possibile continuare ad amministrare il database, ma non è più necessario gestire il motore di database, il sistema operativo server o l'hardware.  Gli esempi di elementi che è possibile continuare a gestire includono i database e gli account di accesso, l'ottimizzazione dell'indice e delle query, nonché il controllo e la sicurezza. Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione e un'amministrazione minime.
- Con **SQL Server in macchine virtuali di Azure**si ha il pieno controllo del sistema operativo e della configurazione dell'istanza di SQL Server. Con una macchina virtuale l'utente può decidere quando aggiornare il sistema operativo e il software del database e quando installare eventuale software aggiuntivo, come l'antivirus. Sono disponibili alcune funzionalità automatizzate che semplificano notevolmente la disponibilità elevata, il backup e l'applicazione di patch. Inoltre, è possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Azure consente di modificare le dimensioni di una macchina virtuale in base alle esigenze. Per informazioni, vedere [Dimensioni delle macchine virtuali e del servizio cloud per Azure](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Contratto di servizio (SLA)
Per molti reparti IT rispettare gli obblighi relativi al tempo di attività di un contratto di servizio è della massima priorità. Questa sezione spiega le condizioni applicate dal contratto di servizio per ogni opzione di hosting del database.

Per il **database SQL**, Microsoft offre un contratto di servizio per la disponibilità del 99,99%. Per le informazioni più recenti, vedere [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/sql-database/). 

Per **SQL Server in esecuzione nelle macchine virtuali di Azure**Microsoft fornisce un contratto di servizio con disponibilità del 99,95% che copre solo alla macchina virtuale. Questo contratto di servizio non comprende i processi, ad esempio SQL Server, in esecuzione nella macchina virtuale e richiede che siano ospitate almeno due istanze di VM in un set di disponibilità. Per le informazioni più recenti, vedere il [Contratto di servizio per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Per la disponibilità elevata del database all'interno delle macchine virtuali, è necessario configurare una delle opzioni di disponibilità elevata supportate in SQL Server, ad esempio [Gruppi di disponibilità AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). L'uso di un'opzione di disponibilità elevata supportata non fornisce un contratto di servizio aggiuntivo, ma permette di ottenere una disponibilità del database superiore al 99,99%.

### <a name="market"></a>È il momento di passare ad Azure
**Server logici del database SQL, pool elastici e database singoli** sono la soluzione ideale per le applicazioni progettate per il cloud quando i fattori critici sono la produttività degli sviluppatori e un time-to-market rapido per le nuove soluzioni. Con la funzionalità di tipo DBA programmatica, è lo strumento ideale per gli architetti e gli sviluppatori cloud perché riduce la gestione del sistema operativo e del database sottostanti. 

**Istanza gestita di database SQL** semplifica notevolmente la migrazione delle applicazioni esistenti al database SQL di Azure, consentendo di immettere sul mercato in Azure in tempi brevi applicazioni di database sottoposte a migrazione.

**SQL Server in esecuzione in macchine virtuali di Azure** è la scelta ideale se le applicazioni nuove o esistenti richiedono database di grandi dimensioni o l'accesso a tutte le funzionalità in SQL Server o in Windows/Linux e si vogliono evitare i tempi e i costi per l'acquisto di nuovo hardware locale. È anche una soluzione appropriata quando si vuole eseguire la migrazione delle applicazioni e dei database locali esistenti in Azure così come sono, nei casi in cui Istanza gestita di database SQL di Azure non sia una scelta indicata. Poiché non è necessario modificare la presentazione, l'applicazione e i livelli dati, si risparmia il tempo e i costi legati a una nuova progettazione dell'architettura della soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sulle ottimizzazioni delle prestazioni eventualmente richieste dalla piattaforma Azure. Per altre informazioni, vedere [Procedure consigliate per le prestazioni di SQL Server nelle macchine virtuali di Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Summary
Questo articolo ha illustrato il database SQL e SQL Server nelle macchine virtuali (VM) di Azure, nonché i vantaggi aziendali comuni che possono influire sulla decisione. Ecco un riepilogo dei suggerimenti da tenere presenti:

Scegliere il **database SQL di Azure** se:

* Si compilano nuove applicazioni basate sul cloud per approfittare della riduzione dei costi e dell'ottimizzazione delle prestazioni offerte dai servizi cloud. Questo approccio offre i vantaggi di un servizio cloud completamente gestito, consente di ridurre il time-to-market iniziale e permette di ottimizzare i costi a lungo termine.
* Si vuole che Microsoft si occupi di operazioni di gestione comuni sui database e si richiedono contratti di servizio con disponibilità più elevata per i database.
* Si vuole eseguire la migrazione di un'applicazione esistente così com'è in Istanza gestita di database SQL di Azure sfruttando l'ulteriore parità con SQL Server e/o le funzionalità di sicurezza e di rete avanzate. Istanza gestita è una soluzione ottimale per le applicazioni nuove ed esistenti.

Scegliere **SQL Server nelle macchine virtuali di Azure** se:

* Sono disponibili applicazioni locali esistenti da estendere nel cloud o di cui si vuole eseguire la migrazione nel cloud oppure si intende compilare applicazioni aziendali di dimensioni superiori a 4 TB. Questo approccio offre il vantaggio di usare la versione e l'edizione di SQL Server preferita, nonché capacità del database elevata, pieno controllo su SQL Server e Windows/Linux e tunneling protetto in locale, oltre a ridurre al minimo i costi per lo sviluppo e la modifica delle applicazioni esistenti.
* Sono disponibili risorse IT esistenti ed è possibile essere il proprietario di operazioni relative ad applicazione di patch, backup e disponibilità elevata del database. Alcune funzionalità automatizzate semplificano notevolmente queste operazioni. 

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione asi database SQL, vedere [Il primo database SQL di Azure](sql-database-get-started-portal.md).
* Vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).
* Per un'introduzione a SQL Server nelle macchine virtuali di Azure, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) .
