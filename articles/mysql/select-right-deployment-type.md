---
title: Selezione del tipo di distribuzione corretto-database di Azure per MySQL
description: Questo articolo descrive i fattori da considerare prima di distribuire database di Azure per MySQL come infrastruttura distribuita come servizio (IaaS) o piattaforma distribuita come servizio (PaaS).
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 000de084cf9375347704cc4d3905ca36bdd77ff8
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926190"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Scegliere l'opzione del server MySQL corretta in Azure

Con Azure, i carichi di lavoro del server MySQL possono essere eseguiti in un'infrastruttura di macchine virtuali ospitata come servizio (IaaS) o come piattaforma ospitata come servizio (PaaS). PaaS dispone di più opzioni di distribuzione e sono disponibili livelli di servizio all'interno di ogni opzione di distribuzione. Quando si sceglie tra IaaS e PaaS, è necessario decidere se si vuole gestire il database, applicare patch ed eseguire backup oppure se si vuole delegare queste operazioni ad Azure.

Quando si decide di prendere in considerazione le due opzioni seguenti:

- **Database di Azure per MySQL** . Questa opzione è un motore di database MySQL completamente gestito basato sulla versione stabile di MySQL Community Edition. Questo database relazionale come servizio (DBaaS), ospitato nella piattaforma cloud di Azure, rientra nella categoria Industry di PaaS.

  Con un'istanza gestita di MySQL in Azure è possibile usare funzionalità predefinite, ovvero l'applicazione automatica di patch, la disponibilità elevata, i backup automatici, la scalabilità elastica, la sicurezza di livello aziendale, la conformità e la governance, il monitoraggio e gli avvisi che altrimenti richiedono una configurazione completa quando MySQL server è in locale o in una VM di Azure. Quando si usa MySQL come servizio, si paga in base al consumo, con le opzioni di scalabilità verticale o orizzontale per un maggiore controllo senza interruzioni.
  
  [Database di Azure per MySQL](overview.md), basato su MySQL Community Edition, è disponibile in due modalità di distribuzione:

  - Un [server singolo](single-server-overview.md) è un servizio di database completamente gestito con requisiti minimi per le personalizzazioni del database. La piattaforma server singolo è progettata per gestire la maggior parte delle funzioni di gestione di database, ad esempio l'applicazione di patch, i backup, la disponibilità elevata e la sicurezza con configurazione e controllo minimi dell'utente. L'architettura è ottimizzata per offrire una disponibilità del 99,99% in una singola zona di disponibilità. I server singoli sono ideali per applicazioni native per il cloud progettate per gestire l'applicazione automatizzata di patch senza la necessità di un controllo granulare sulla pianificazione dell'applicazione di patch e sulle impostazioni di configurazione di MySQL personalizzate.

  - [Flexible Server (anteprima)](flexible-server/overview.md) è un servizio di database completamente gestito progettato per offrire un controllo più granulare e una maggiore flessibilità rispetto alle impostazioni di configurazione e alle funzioni di gestione del database. In generale, il servizio offre maggiore flessibilità e personalizzazioni della configurazione del server rispetto alla distribuzione a server singolo in base ai requisiti dell'utente. L'architettura di server flessibili consente agli utenti di scegliere una disponibilità elevata all'interno di una singola zona di disponibilità e in più zone di disponibilità. I server flessibili forniscono anche controlli di ottimizzazione dei costi migliori con la possibilità di avviare/arrestare il server e gli SKU a espansione, ideali per i carichi di lavoro che non necessitano di capacità di calcolo completa in modo continuo.

  I server flessibili sono ideali per:

  - Sviluppo di applicazioni che richiedono un migliore controllo e personalizzazioni del motore MySQL.
  - Disponibilità elevata di ridondanza della zona
  - Finestre di manutenzione gestite

- **MySQL in macchine virtuali di Azure** . Questa opzione rientra nella categoria Industry di IaaS. Con questo servizio è possibile eseguire MySQL server all'interno di una macchina virtuale gestita nella piattaforma cloud di Azure. Tutte le versioni e le edizioni recenti di MySQL possono essere installate nella macchina virtuale.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Confronto tra le opzioni di distribuzione di MySQL in Azure

Le differenze principali tra queste opzioni sono elencate nella tabella seguente:

| Attributo          | Database di Azure per MySQL<br/>Server unico |Database di Azure per MySQL<br/>Server flessibile  |MySQL in macchine virtuali di Azure                      |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:---------------------------------------|
| Supporto della versione di MySQL | 5,6, 5,7 & 8,0| 5.7 | Qualsiasi versione|
| Ridimensionamento delle risorse di calcolo | Supportato (il ridimensionamento da e verso il livello Basic non è supportato)| Supportato | Supportato|
| Dimensioni dello spazio di archiviazione | da 5 GiB a 16 TiB| da 5 GiB a 16 TiB | 32 GiB a 32.767 GiB|
| Scalabilità dell'archiviazione online | Supportato| Supportato| Non supportate|
| Ridimensionamento automatico dell'archiviazione | Supportato| Non supportato nella versione di anteprima| Non supportate|
| Connettività di rete | -Endpoint pubblici con firewall del server.<br/> -Accesso privato con supporto del collegamento privato.|-Endpoint pubblici con firewall del server.<br/> -Accesso privato con l'integrazione della rete virtuale.| -Endpoint pubblici con firewall del server.<br/> -Accesso privato con supporto del collegamento privato.|
| Contratto di servizio | CONTRATTO di disponibilità del 99,99% |Nessun contratto di contratto in anteprima| 99,99% con zone di disponibilità|
| Applicazione di patch al sistema operativo| Automatico  | Automatico con controllo finestra di manutenzione personalizzata | Gestito dagli utenti finali |
| Patch di MySQL     | Automatico  | Automatico con controllo finestra di manutenzione personalizzata | Gestito dagli utenti finali |
| Disponibilità elevata | Disponibilità elevata incorporata all'interno di una singola zona di disponibilità| Disponibilità elevata incorporata all'interno e tra le zone di disponibilità | Gestione personalizzata mediante clustering, replica e così via.|
| Ridondanza della zona | Non supportate | Supportato | Supportato|
| Scenari ibridi | Supportato con [replica dei dati in ingresso](./concepts-data-in-replication.md)| Non disponibile in anteprima | Gestito dagli utenti finali |
| Repliche in lettura | Supportato (fino a 5 repliche)| Supportato (fino a 10 repliche)| Gestito dagli utenti finali |
| Backup | Automatizzato con conservazione di 7-35 giorni | Automatizzato con conservazione di 1-35 giorni | Gestito dagli utenti finali |
| Monitoraggio delle operazioni del database | Supportato | Supportato | Gestito dagli utenti finali |
| Ripristino di emergenza | Supportato con archiviazione di backup con ridondanza geografica e repliche di lettura tra più aree | Non supportato nella versione di anteprima| Gestione personalizzata con tecnologie di replica |
| Analisi delle prestazioni della query | Supportato | Non disponibile in anteprima| Gestito dagli utenti finali |
| Prezzi delle istanze riservate | Supportato | Non disponibile in anteprima | Supportato |
| Autenticazione di Azure AD | Supportato | Non disponibile in anteprima | Non supportato|
| Crittografia dei dati inattivi | Supportato con chiavi gestite dal cliente | Supportato con chiavi gestite dal servizio | Non supportato|
| SSL/TLS | Abilitata per impostazione predefinita con supporto per TLS v 1.2, 1,1 e 1,0 | Applicato con TLS v 1.2 | Supportato con TLS v 1.2, 1,1 e 1,0 |
| Gestione della flotta | Supportato con l'interfaccia della riga di comando di Azure, PowerShell, REST e Azure Resource Manager | Supportato con l'interfaccia della riga di comando di Azure, PowerShell, REST e Azure Resource Manager  | Supportato per le macchine virtuali con l'interfaccia della riga di comando di Azure, PowerShell, REST e Azure Resource Manager |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivazioni aziendali per la scelta di PaaS o IaaS

Esistono diversi fattori che possono influenzare la decisione di scegliere PaaS o IaaS per ospitare i database MySQL.

### <a name="cost"></a>Costi

La riduzione dei costi è spesso la considerazione principale che determina la soluzione migliore per ospitare i database. Ciò vale sia per le startup con scarse disponibilità economiche, sia per team di una società consolidata che operano con vincoli di budget limitati. Questa sezione descrive le nozioni di base relative a fatturazione e licenze in Azure che si applicano a database di Azure per MySQL e MySQL in macchine virtuali di Azure.

#### <a name="billing"></a>Fatturazione

Database di Azure per MySQL è attualmente disponibile come servizio in diversi livelli con prezzi diversi per le risorse. Tutte le risorse vengono fatturate su base oraria a una tariffa fissa. Per le informazioni più recenti sui livelli di servizio, le dimensioni di calcolo e gli importi di archiviazione attualmente supportati, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/). È possibile modificare dinamicamente i livelli di servizio e le dimensioni di calcolo in base alle esigenze di velocità effettiva diverse dell'applicazione. Viene fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/) normale.

Con database di Azure per MySQL, Microsoft configura, patch e aggiorna automaticamente il software del database. Queste azioni automatiche riducono i costi amministrativi. Inoltre, database di Azure per MySQL offre funzionalità di [backup automatici](./concepts-backup.md) . Le funzionalità di backup predefinite consentono di ottenere una significativa riduzione dei costi, specialmente per un numero elevato di database. Con MySQL in macchine virtuali di Azure è invece possibile scegliere ed eseguire qualsiasi versione di MySQL. Indipendentemente dalla versione di MySQL che usi, paghi per la VM di cui è stato effettuato il provisioning, i costi di archiviazione associati ai dati, il backup, il monitoraggio dei dati e l'archiviazione dei log e i costi per il tipo di licenza MySQL specifico usato (se presente).

Database di Azure per MySQL offre disponibilità elevata incorporata per qualsiasi tipo di interruzione a livello di nodo mantenendo al tempo stesso la garanzia del contratto di servizio del 99,99% per il servizio. Tuttavia, per la disponibilità elevata del database all'interno delle VM, è possibile usare le opzioni di disponibilità elevata, ad esempio la [replica MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication.html) disponibili in un database MySQL. L'uso di un'opzione di disponibilità elevata supportata non fornisce un contratto di servizio aggiuntivo, ma permette di ottenere una disponibilità del database superiore al 99,99% a costi aggiuntivi e carichi amministrativi.

Per altre informazioni sui prezzi, vedere gli articoli seguenti:

- [Prezzi per database di Azure per MySQL](https://azure.microsoft.com/pricing/details/mysql/)
- [Prezzi per le macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Amministrazione

Per molte aziende, la decisione di passare a un servizio cloud riguarda sia la possibilità di ridurre la complessità dell'amministrazione, sia i costi.

Con IaaS, Microsoft:

- Amministra l'infrastruttura sottostante.
- Fornisce l'applicazione automatica delle patch per l'hardware e il sistema operativo sottostanti.
  
Con PaaS, Microsoft:

- Amministra l'infrastruttura sottostante.
- Fornisce l'applicazione automatica delle patch per l'hardware, il sistema operativo e il motore di database sottostanti.
- Gestisce la disponibilità elevata del database.
- Esegue automaticamente i backup e replica tutti i dati per fornire il ripristino di emergenza.
- Crittografa i dati inattivi e in movimento per impostazione predefinita.
- Monitora il server e fornisce funzionalità per informazioni dettagliate sulle prestazioni delle query e consigli sulle prestazioni

Nell'elenco seguente vengono descritte le considerazioni amministrative per ogni opzione:

- Con database di Azure per MySQL è possibile continuare a amministrare il database. Tuttavia, non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Esempi di elementi che è possibile continuare ad amministrare includono:

  - Database
  - Accesso
  - Ottimizzazione indici
  - Ottimizzazione delle query
  - Controllo
  - Sicurezza

  Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione e un'amministrazione da minime a nulle.

- Con MySQL in macchine virtuali di Azure si ha il controllo completo del sistema operativo e della configurazione dell'istanza di MySQL server. Con una macchina virtuale, si decide quando eseguire l'aggiornamento o l'upgrade del sistema operativo e del software del database e quali patch applicare. Si decide inoltre quando installare software aggiuntivi, ad esempio un'applicazione antivirus. Sono disponibili alcune funzionalità automatizzate che semplificano notevolmente la disponibilità elevata, il backup e l'applicazione di patch. È possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Per altre informazioni, vedere la pagina [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>È il momento di passare ad Azure

- Database di Azure per MySQL è la soluzione ideale per le applicazioni progettate per il cloud quando la produttività degli sviluppatori e il time-to-Market rapido per le nuove soluzioni sono cruciali. Con la funzionalità programmatica che è come DBA, il servizio è adatto agli architetti e gli sviluppatori cloud perché riduce la gestione del sistema operativo e del database sottostanti.

- Quando si vuole evitare il tempo e la spesa per l'acquisizione di un nuovo hardware locale, MySQL in macchine virtuali di Azure è la soluzione ideale per le applicazioni che richiedono un controllo granulare e la personalizzazione del motore MySQL non supportato dal servizio o che richiedono l'accesso al sistema operativo sottostante. Questa soluzione è adatta anche per la migrazione dei database e delle applicazioni locali esistenti in Azure intatta, per i casi in cui database di Azure per MySQL è inadatto.

Poiché non è necessario modificare i livelli di presentazione, applicazione e dati, è possibile risparmiare tempo e budget per riprogettare la soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sulla risoluzione di alcune ottimizzazioni delle prestazioni che potrebbero essere richieste dalla piattaforma Azure.

## <a name="next-steps"></a>Passaggi successivi

- Vedi [i prezzi di database di Azure per MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
- Iniziare [creando il primo server](./quickstart-create-mysql-server-database-using-azure-portal.md).
