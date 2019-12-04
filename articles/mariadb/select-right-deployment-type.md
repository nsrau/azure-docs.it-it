---
title: Selezione del tipo di distribuzione corretto-database di Azure per MariaDB
description: Questo articolo descrive i fattori da considerare prima di distribuire database di Azure per MariaDB come infrastruttura come servizio (IaaS) o piattaforma distribuita come servizio (PaaS).
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d53a3f270c1de3c8d533b5886e0051f6aa1ef10d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776739"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Scegliere l'opzione del server MariaDB corretta in Azure

Con Azure, i carichi di lavoro del server MariaDB possono essere eseguiti in un'infrastruttura di macchina virtuale ospitata come servizio (IaaS) o come piattaforma ospitata come servizio (PaaS). PaaS dispone di più opzioni di distribuzione e sono disponibili livelli di servizio all'interno di ogni opzione di distribuzione. Quando si sceglie tra IaaS e PaaS, è necessario decidere se si desidera gestire il database, applicare patch ed eseguire backup oppure se si desidera delegare queste operazioni ad Azure.

Quando si decide di prendere in considerazione le due opzioni seguenti:

- **Database di Azure per MariaDB:** Questa opzione è un motore di database MariaDB completamente gestito basato sulla versione stabile di MariaDB Community Edition. Questo database relazionale come servizio (DBaaS), ospitato nella piattaforma cloud di Azure, rientra nella categoria Industry di PaaS.

  Con un'istanza gestita di MariaDB in Azure è possibile usare funzionalità predefinite che altrimenti richiedono una configurazione completa quando il server MariaDB è in locale o in una VM di Azure.

  Quando si usa MariaDB come servizio, si paga con le opzioni di scalabilità verticale o orizzontale per un maggiore controllo senza interruzioni. A differenza del server MariaDB autonomo, il database di Azure per MariaDB offre funzionalità aggiuntive come la disponibilità elevata, l'intelligence e la gestione predefinite.

- **MariaDB in macchine virtuali di Azure:** Questa opzione rientra nella categoria Industry di IaaS. Con questo servizio è possibile eseguire il server MariaDB all'interno di una macchina virtuale completamente gestita nella piattaforma cloud di Azure. Tutte le versioni e le edizioni recenti di MariaDB possono essere installate in una macchina virtuale IaaS.

  Nella differenza più significativa rispetto al database di Azure per MariaDB, MariaDB in macchine virtuali di Azure offre il controllo sul motore di database. Tuttavia, questo controllo è responsabile della gestione delle macchine virtuali e di molte attività di amministrazione di database (DBA). Queste attività includono la manutenzione e l'applicazione di patch ai server di database, al recupero del database e alla progettazione a disponibilità elevata.

Le differenze principali tra queste opzioni sono elencate nella tabella seguente:

|            | Database di Azure per MariaDB | MariaDB in macchine virtuali di Azure    |
|:-------------------|:-----------------------------|:--------------------|
| Contratto di servizio                | Offre SLA con disponibilità del 99,99%| Disponibilità fino al 99,95% con due o più istanze nello stesso set di disponibilità.<br/><br/>disponibilità del 99,9% con una macchina virtuale a istanza singola che usa archiviazione Premium.<br/><br/>99,99% con zone di disponibilità con più istanze in più set di disponibilità.<br/><br/>Vedere il [contratto di contratto per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Applicazione di patch al sistema operativo        | Automatico  | Gestito dai clienti |
| Applicazione di patch MariaDB     | Automatico  | Gestito dai clienti |
| Disponibilità elevata | Il modello a disponibilità elevata è basato sui meccanismi di failover predefiniti per quando si verifica un'interruzione a livello di nodo. In questi casi, il servizio crea automaticamente una nuova istanza e connette lo spazio di archiviazione a questa istanza. | I clienti progettano, implementano, testano e gestiscono la disponibilità elevata. Le funzionalità possono includere il clustering di failover always on, la replica di gruppi always on, log shipping o la replica transazionale.|
| Ridondanza della zona | Attualmente non supportati | Le macchine virtuali di Azure possono essere configurate per l'esecuzione in diverse zone di disponibilità. Per una soluzione locale, i clienti devono creare, gestire e gestire i propri data center secondari.|
| Scenari ibridi | Con [replica dei dati in ingresso](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication)è possibile sincronizzare i dati da un server MariaDB esterno nel database di Azure per il servizio MariaDB. Il server esterno può trovarsi in locale, in macchine virtuali, o essere un servizio di database ospitato da altri provider di servizi cloud.<br/><br/> Con la funzionalità di [lettura della replica](https://docs.microsoft.com/azure/mariadb/concepts-read-replicas) è possibile replicare i dati da un database di Azure per il server master MariaDB a un massimo di cinque server di replica di sola lettura. Le repliche si trovano nella stessa area di Azure o in aree diverse. Le repliche di sola lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica binlog.<br/><br/>La replica di lettura tra aree è attualmente disponibile in anteprima pubblica.| Gestito dai clienti
| Backup e ripristino | Crea automaticamente [backup del server](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) e li archivia in una risorsa di archiviazione configurata dall'utente con ridondanza locale o con ridondanza geografica. Il servizio accetta backup completi, differenziali e del log delle transazioni | Gestito dai clienti |
| Monitoraggio delle operazioni del database | Offre ai clienti la possibilità di [impostare avvisi](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) sull'operazione del database e di agire al raggiungimento delle soglie. | Gestito dai clienti |
| Advanced Threat Protection | Offre [protezione avanzata dalle minacce](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal). Questa protezione rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.<br/><br/>Advanced Threat Protection è attualmente disponibile in anteprima pubblica.| I clienti devono compilare questa protezione autonomamente.
| Ripristino di emergenza | Archivia i backup automatici nell'archiviazione con ridondanza [locale o con ridondanza geografica](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal)configurata dall'utente. I backup possono inoltre ripristinare un server a un momento specifico. Il periodo di memorizzazione è da 7 a 35 giorni. Il ripristino viene eseguito tramite il portale di Azure. | Completamente gestito dai clienti. Le responsabilità includono ma non sono limitate a pianificazione, test, archiviazione, archiviazione e conservazione. Un'altra opzione consiste nell'usare un insieme di credenziali di servizi di ripristino di Azure per eseguire il backup di VM e database di Azure nelle VM. Questa opzione è in anteprima. |
| Raccomandazioni per le prestazioni | Fornisce ai clienti [consigli sulle prestazioni](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) in base ai file di log dell'utilizzo generati dal sistema. Le indicazioni consentono di ottimizzare i carichi di lavoro.<br/><br/>Le raccomandazioni sulle prestazioni sono attualmente disponibili in anteprima pubblica. | Gestito dai clienti |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivazioni aziendali per la scelta di PaaS o IaaS

Esistono diversi fattori che possono influenzare la decisione di scegliere PaaS o IaaS per ospitare i database MariaDB.

### <a name="cost"></a>Costo

Un finanziamento limitato è spesso la considerazione principale che determina la migliore soluzione per l'hosting dei database. Si tratta di un avvio con pochi soldi o un team in una società stabilita che opera con vincoli di budget limitati. Questa sezione descrive le nozioni di base relative a fatturazione e licenze in Azure che si applicano al database di Azure per MariaDB e MariaDB nelle macchine virtuali di Azure.

#### <a name="billing"></a>Fatturazione

Il database di Azure per MariaDB è attualmente disponibile come servizio in diversi livelli con prezzi diversi per le risorse. Tutte le risorse vengono fatturate su base oraria a una tariffa fissa. Per le informazioni più recenti sui livelli di servizio, le dimensioni di calcolo e gli importi di archiviazione attualmente supportati, vedere [modello di acquisto basato su vCore](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). È possibile modificare dinamicamente i livelli di servizio e le dimensioni di calcolo in base alle esigenze di velocità effettiva diverse dell'applicazione. Viene addebitato il traffico Internet in uscita a normali [tariffe di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/).

Con database di Azure per MariaDB, Microsoft configura, patch e aggiorna automaticamente il software del database. Queste azioni automatiche riducono i costi amministrativi. Inoltre, il database di Azure per MariaDB offre funzionalità [di backup predefinite](https://docs.microsoft.com/azure/MariaDB/concepts-backup) . Queste funzionalità consentono di ottenere risparmi significativi sui costi, soprattutto quando si dispone di un numero elevato di database. Al contrario, con MariaDB nelle macchine virtuali di Azure è possibile scegliere ed eseguire qualsiasi versione di MariaDB. Indipendentemente dalla versione di MariaDB in uso, paghi per la macchina virtuale di cui è stato effettuato il provisioning e i costi per il tipo di licenza MariaDB specifico usato.

Database di Azure per MariaDB offre disponibilità elevata incorporata per qualsiasi tipo di interruzione a livello di nodo mantenendo al tempo stesso la garanzia del contratto di servizio del 99,99% per il servizio. Tuttavia, per la disponibilità elevata del database all'interno delle VM, i clienti devono usare le opzioni di disponibilità elevata, ad esempio la [replica MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/) , disponibili in un database MariaDB. L'uso di un'opzione di disponibilità elevata supportata non prevede un contratto di servizi aggiuntivo. Tuttavia, consente di ottenere una disponibilità del database superiore al 99,99% a costi aggiuntivi e sovraccarico amministrativo.

Per ulteriori informazioni sui prezzi, vedere gli articoli seguenti:
* [Prezzi per database di Azure per MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Prezzi di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

Per molte aziende, la decisione di passare a un servizio cloud è la quantità di spazio di ripartizione della complessità dell'amministrazione, in quanto si tratta di costi. Con IaaS e PaaS, Microsoft:

- Amministra l'infrastruttura sottostante.
- Replica automaticamente tutti i dati per fornire il ripristino di emergenza.
- Configura e aggiorna il software del database.
- Gestisce il bilanciamento del carico.
- Esegue un failover trasparente in caso di errore del server.

Nell'elenco seguente vengono descritte le considerazioni amministrative per ogni opzione:

* Con database di Azure per MariaDB è possibile continuare ad amministrare il database. Ma non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Esempi di elementi che è possibile continuare ad amministrare includono:

  - Database
  - Accesso
  - Ottimizzazione degli indici
  - Ottimizzazione query
  - Controllo
  - Sicurezza

  Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione o un'amministrazione minima.

* Con MariaDB in macchine virtuali di Azure, si ha il controllo completo del sistema operativo e della configurazione dell'istanza del server MariaDB. Con una macchina virtuale, si decide quando aggiornare o aggiornare il sistema operativo e il software del database. Si decide inoltre quando installare software aggiuntivo, ad esempio un'applicazione antivirus. Alcune funzionalità automatiche sono fornite per semplificare notevolmente l'applicazione di patch, backup e disponibilità elevata. È possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Per ulteriori informazioni, vedere [la pagina relativa alle dimensioni delle macchine virtuali e dei servizi cloud per Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="time-to-move-to-azure"></a>Tempo di spostamento in Azure

* Database di Azure per MariaDB è la soluzione ideale per le applicazioni progettate per il cloud quando la produttività degli sviluppatori e il time-to-Market rapido per le nuove soluzioni sono cruciali. Con la funzionalità a livello di codice, ad esempio DBA, il servizio è adatto per architetti e sviluppatori di cloud perché riduce la necessità di gestire il sistema operativo e il database sottostanti.

* Quando si vuole evitare il tempo e i costi di acquisizione di un nuovo hardware locale, MariaDB in macchine virtuali di Azure è la soluzione ideale per le applicazioni che richiedono un database MariaDB o l'accesso alle funzionalità di MariaDB in Windows o Linux. Questa soluzione è adatta anche per la migrazione di database e applicazioni locali esistenti in Azure intatta, per i casi in cui il database di Azure per MariaDB non è adatto.

  Poiché non è necessario modificare i livelli di presentazione, applicazione e dati, è possibile risparmiare tempo e budget per riprogettare la soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sulla risoluzione di alcune ottimizzazioni delle prestazioni che potrebbero essere richieste dalla piattaforma Azure.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [i prezzi di database di Azure per MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/).
* Iniziare [creando il primo server](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).
