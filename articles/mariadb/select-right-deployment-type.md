---
title: Selezione del tipo di distribuzione corretto per il database di Azure per MariaDB
description: Questo articolo descrive le considerazioni che è necessario eseguire prima di procedere con l'infrastruttura distribuita come servizio (IaaS) o PaaS (Platform-as-a-Service) per il database di Azure per MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817436"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Scegliere l'opzione del server MariaDB corretta in Azure

Con Azure, i carichi di lavoro del server MariaDB possono essere eseguiti in una macchina virtuale di infrastruttura host (IaaS) o come servizio ospitato (PaaS). PaaS dispone di più opzioni di distribuzione e sono disponibili livelli di servizio all'interno di ogni opzione di distribuzione. Quando si decide tra PaaS o IaaS, è necessario determinare se si vuole gestire il database, applicare patch ed eseguire backup oppure delegare queste operazioni ad Azure.</br>

In base alla risposta a questa domanda, prendere in considerazione le opzioni seguenti: <br/>

**Database di Azure per MariaDB** è un motore di database MariaDB completamente gestito basato sulla versione stabile della Community Edition. Questo database relazionale come servizio (DBaaS), ospitato nel cloud di Azure, rientra nella categoria Industry della piattaforma distribuita come servizio (PaaS). Con un'istanza gestita di MariaDB in Azure è possibile usare le funzionalità e le funzionalità predefinite che richiedono una configurazione completa quando si usa il server MariaDB (in locale o in una macchina virtuale di Azure). Quando si usa MariaDB come servizio, viene effettuato il pagamento in base al consumo con le opzioni per aumentare o ridurre la potenza senza interruzioni. Inoltre, a differenza del server MariaDB autonomo, database di Azure per MariaDB offre funzionalità aggiuntive, ad esempio disponibilità elevata, Intelligence e gestione predefinite. <br/><br/>
**MariaDB nella** macchina virtuale di Azure rientra nella categoria di settore Infrastructure-as-a-Service (IaaS) e consente di eseguire il server MariaDB all'interno di una macchina virtuale completamente gestita nel cloud di Azure. Tutte le versioni e le edizioni recenti di MariaDB possono essere installate in una macchina virtuale IaaS. La differenza più significativa rispetto al database di Azure per MariaDB è che MariaDB in macchine virtuali di Azure consentono di controllare il motore di database. Tuttavia, questo controllo comporta un costo aggiuntivo per la gestione delle macchine virtuali e di numerose attività DBA come la manutenzione/applicazione di patch del server di database, il ripristino e la progettazione della disponibilità elevata e così via.

Le differenze principali tra queste opzioni sono elencate nella tabella seguente:

|            | **Database di Azure per MariaDB** | **MariaDB in macchine virtuali di Azure**    |
|:-------------------|:-----------------------------|:--------------------|
| **Contratto di servizio**                | Offre SLA con disponibilità del 99,99%| Disponibilità fino al 99,95% con due o più istanze nello stesso set di disponibilità. <br/>99,9% di VM a istanza singola con archiviazione Premium <br/> 99,99% con la zona di disponibilità con due o più istanze in 2 o più set di disponibilità.<br/> Nota: [contratto di contratto macchina virtuale](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **Applicazione di patch al sistema operativo**        | Automatico  | Gestito dai clienti |
|**Applicazione di patch MariaDB**     | Automatico  | Gestito dai clienti |
| **Disponibilità elevata** | Il modello di disponibilità elevata si basa sui meccanismi di failover predefiniti quando si verifica un'interruzione a livello di nodo. In questi casi, il servizio crea automaticamente un'istanza di e connette lo spazio di archiviazione alla nuova istanza. | La disponibilità elevata è progettata, implementata, testata e gestita dal cliente. Ciò può includere la replica di tipo always on (clustering di failover o di gruppo), il log shipping e la replica transazionale, a seconda della versione del motore MariaDB in uso.|
| **Ridondanza della zona** | Attualmente non supportato. | Le macchine virtuali di Azure possono essere configurate per l'esecuzione in diverse zone di disponibilità. Per una soluzione locale, è previsto che i clienti creino, gestiscano e gestiscano i propri data center secondari.|
| **Scenari ibridi** | [Replica dei dati in ingresso](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication) consente di sincronizzare i dati da un server MariaDB esterno nel database di Azure per il servizio MariaDB. Il server esterno può trovarsi in locale, in macchine virtuali, o essere un servizio di database ospitato da altri provider di servizi cloud.  <br/> <br/> La funzionalità di [lettura della replica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) consente di replicare i dati da un database di Azure per il server MariaDB (Master) a un massimo di cinque server di sola lettura (repliche) nella stessa area di Azure o in più aree. Le repliche di sola lettura vengono aggiornate in modo asincrono tramite la tecnologia di replica binlog.   <br/> <br/> Nota: la replica di lettura tra aree è attualmente disponibile in anteprima pubblica.| Gestito dai clienti <br/>
| **Backup e ripristino** | Crea automaticamente [backup del server](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) e li archivia in un archivio con ridondanza locale o con ridondanza geografica configurato dall'utente. Il servizio accetta backup completi, differenziali e del log delle transazioni | Gestito dai clienti |
| **Monitoraggio delle operazioni del database** | Consente ai clienti di [impostare avvisi](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) sull'operazione del database e di agire al raggiungimento delle soglie. | Gestito dai clienti |
| **Protezione avanzata thread** | Fornisce una [protezione avanzata dalle minacce](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal) che rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere ai database o sfruttarli. <br/> <br/> Nota: la protezione avanzata thread è attualmente disponibile in anteprima pubblica.| I clienti devono compilare per se stessi.
| **Backup (ripristino di emergenza)** | Archivia i backup automatici in un utente configurato in un archivio con ridondanza [locale o con ridondanza geografica](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal). È inoltre possibile utilizzare i backup per ripristinare un server a un punto nel tempo. Il periodo di memorizzazione può essere impostato da 7-35 giorni. Il ripristino può essere eseguito usando il portale di Azure. <br/> | Completamente gestito dal cliente, inclusi, a titolo esemplificativo, pianificazione, test, archiviazione, archiviazione e conservazione. Un'altra opzione consiste nell'usare l'insieme di credenziali di servizi di ripristino di Azure per eseguire il backup di VM e database di Azure nelle VM (in anteprima). |
| **Raccomandazione per le prestazioni** | Fornisce ai clienti [consigli sulle prestazioni](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) proattive basati sui dati di telemetria sull'utilizzo che consentono di ottimizzare i carichi di lavoro. <br/> <br/> Nota: la raccomandazione per le prestazioni è attualmente disponibile in anteprima pubblica. | Gestito dai clienti |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivazioni aziendali per la scelta di PaaS o IaaS

Esistono diversi fattori che possono influenzare la decisione di scegliere PaaS o IaaS per ospitare i database MariaDB:

### <a name="cost"></a>Costo

Che si tratti di un avvio vincolato per denaro o un team in una società stabilita che opera con vincoli di budget limitati, il finanziamento limitato è spesso la considerazione principale quando si determina la soluzione migliore per l'hosting dei database. Questa sezione descrive le nozioni di base relative a fatturazione e licenze in Azure per quanto riguarda database di Azure per MariaDB e MariaDB in macchine virtuali di Azure:

#### <a name="billing"></a>Fatturazione

Attualmente, il database di Azure per MariaDB è disponibile come servizio in diversi livelli con prezzi diversi per le risorse, tutte fatturate ogni ora a una tariffa fissa. Per le informazioni più recenti sui livelli di servizio, le dimensioni di calcolo e gli importi di archiviazione correnti supportati, vedere [modello di acquisto basato su vCore](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers). È possibile modificare in modo dinamico i livelli di servizio e le dimensioni di calcolo per soddisfare le diverse esigenze di velocità effettiva dell'applicazione. Viene addebitato il traffico Internet in uscita a normali [tariffe di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/).

Con database di Azure per MariaDB, il software del database viene automaticamente configurato, applicato a patch e aggiornato da Microsoft, riducendo i costi amministrativi. Le funzionalità di [backup predefinite](https://docs.microsoft.com/azure/MariaDB/concepts-backup) consentono anche di ottenere una significativa riduzione dei costi, specialmente per un numero elevato di database. Con MariaDB in macchine virtuali di Azure è possibile scegliere ed eseguire una qualsiasi versione di MariaDB. Indipendentemente dalla versione di MariaDB in uso, paghi per la macchina virtuale di cui è stato effettuato il provisioning e i costi per il tipo di licenza specifico usato per MariaDB.

Il database di Azure per MariaDB offre disponibilità elevata incorporata per qualsiasi tipo di interruzione a livello di nodo e mantiene comunque il contratto di servizio del 99,99% per il servizio. Tuttavia, per la disponibilità elevata del database all'interno delle macchine virtuali, il cliente deve passare attraverso le opzioni di disponibilità elevata disponibili nel database MariaDB, ad esempio la [replica di MariaDB](https://mariadb.com/kb/en/library/setting-up-replication/). L'uso di un'opzione di disponibilità elevata supportata non prevede un contratto di servizi aggiuntivo, ma consente di ottenere > disponibilità di database del 99,99% a un costo aggiuntivo e un sovraccarico amministrativo.

Per altre informazioni sui prezzi, vedere le seguenti risorse:
* [Prezzi per database di Azure per MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Prezzi di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Amministrazione

Per molte aziende, la decisione di passare a un servizio cloud è la quantità di spazio di ripartizione per l'amministrazione. Con IaaS e PaaS, Microsoft amministra l'infrastruttura sottostante e replica automaticamente tutti i dati per fornire il ripristino di emergenza, configura e aggiorna il software del database, gestisce il bilanciamento del carico ed esegue un failover trasparente in caso di errore del server.

* **Con database di Azure per MariaDB**è possibile continuare a amministrare il database, ma non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Gli esempi di elementi che è possibile continuare a gestire includono i database e gli account di accesso, l'ottimizzazione dell'indice e delle query, nonché il controllo e la sicurezza. Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione o un'amministrazione minima o nulla.<br/><br/>
* **Con MariaDB in macchine virtuali di Azure**si ha il controllo completo del sistema operativo e della configurazione dell'istanza del server MariaDB. Con una macchina virtuale, si decide quando aggiornare o aggiornare il sistema operativo e il software del database e quando installare software aggiuntivo, ad esempio un'applicazione antivirus. Sono disponibili alcune funzionalità automatizzate che semplificano notevolmente la disponibilità elevata, il backup e l'applicazione di patch. Inoltre, è possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Per informazioni, vedere dimensioni delle macchine virtuali e dei servizi cloud per Azure.

### <a name="time-to-move-to-azure-br"></a>Tempo di spostamento in Azure <br/>
* **Database di Azure per MariaDB** è la soluzione ideale per le applicazioni progettate per il cloud quando la produttività degli sviluppatori e il time-to-Market rapido per le nuove soluzioni sono cruciali. Con la funzionalità a livello di DBA, il servizio è perfetto per architetti e sviluppatori di cloud perché riduce la necessità di gestire il sistema operativo e il database sottostanti.<br/><br/>
* **MariaDB in macchine virtuali di Azure** è perfetto per le applicazioni nuove o esistenti che richiedono il database MariaDB o l'accesso alle funzionalità del database MariaDB in Windows/Linux e si vuole evitare il tempo e i costi di acquisizione di nuovi componenti hardware locali. Questa opzione è ideale anche per la migrazione di database e applicazioni locali esistenti in Azure così come sono, per i casi in cui un'istanza di database di Azure per MariaDB non è ottimale. Poiché non è necessario modificare i livelli di presentazione, applicazione e dati, è possibile risparmiare tempo e budget per riprogettare la soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sulla risoluzione di alcune ottimizzazioni delle prestazioni che potrebbero essere richieste dalla piattaforma Azure.

## <a name="next-steps"></a>Passaggi successivi

* Vedi [i prezzi di database di Azure per MariaDB](https://azure.microsoft.com/pricing/details/MariaDB/)
* Iniziare [creando il primo server](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).

