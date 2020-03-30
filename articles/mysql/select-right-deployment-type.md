---
title: Selezione del tipo di distribuzione corretto - Database di Azure per MySQLSelecting the right deployment type - Azure Database for MySQL
description: Questo articolo descrive i fattori da considerare prima di distribuire Database di Azure per MySQL come infrastruttura come servizio (IaaS) o piattaforma come servizio (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255539"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Scegliere l'opzione MySQL Server appropriata in AzureChoose the right MySQL Server option in Azure

Con Azure, i carichi di lavoro del server MySQL possono essere eseguiti in un'infrastruttura di macchina virtuale ospitata come servizio (IaaS) o come piattaforma ospitata come servizio (PaaS). PaaS ha più opzioni di distribuzione e sono disponibili livelli di servizio all'interno di ogni opzione di distribuzione. Quando si sceglie tra IaaS e PaaS, è necessario decidere se si desidera gestire il database, applicare patch ed eseguire backup oppure se si desidera delegare queste operazioni ad Azure.

Quando si prende una decisione, considerare le due opzioni seguenti:

- **Database di Azure per MySQL**. Questa opzione è un motore di database MySQL completamente gestito basato sulla versione stabile di MySQL Community Edition. Questo database relazionale come servizio (DBaaS), ospitato nella piattaforma cloud di Azure, rientra nella categoria di settore di PaaS.This relational database as a service (DBaaS), hosted on the Azure cloud platform, falls in the industry category of PaaS.

  Con un'istanza gestita di MySQL in Azure, è possibile usare funzionalità predefinite che altrimenti richiedono una configurazione estesa quando MySQL Server è in locale o in una macchina virtuale di Azure.With a managed instance of MySQL on Azure, you can use built-in features that otherwise require extensive configuration when MySQL Server is either on-premises or in an Azure VM.

  Quando si utilizza MySQL come servizio, si paga come si va con le opzioni per scalare o scalare orizzontalmente per un maggiore controllo senza interruzioni. E a differenza di MySQL Server autonomo, Il database di Azure per MySQL include funzionalità aggiuntive, ad esempio disponibilità elevata, intelligenza e gestione integrate.

- **MySQL in macchine virtuali di Azure**. Questa opzione rientra nella categoria di settore di IaaS. Con questo servizio, è possibile eseguire MySQL Server all'interno di una macchina virtuale completamente gestita nella piattaforma cloud di Azure.With this service, you can run MySQL Server inside a fully managed virtual machine on the Azure cloud platform. Tutte le versioni e le edizioni recenti di MySQL possono essere installate su una macchina virtuale IaaS.

  Nella differenza più significativa rispetto al database di Azure per MySQL, MySQL in macchine virtuali di Azure offre il controllo sul motore di database. Tuttavia, questo controllo ha il costo della responsabilità di gestire le macchine virtuali e molte attività di amministrazione del database (DBA). Queste attività includono la gestione e l'applicazione di patch ai server di database, il ripristino del database e la progettazione a disponibilità elevata.

Le differenze principali tra queste opzioni sono elencate nella tabella seguente:

|            | Database di Azure per MySQL | MySQL on Azure VMs    |
|:-------------------|:-----------------------------|:--------------------|
| Contratto di servizio (SLA)                | Offre un SLA di disponibilità del 99,99%| Disponibilità fino al 99,95% con due o più istanze nello stesso set di disponibilità.<br/><br/>Disponibilità del 99,9% con una singola istanza di VM tramite archiviazione Premium.99.9% availability with a single instance VM using premium storage.<br/><br/>99,99% utilizzando zone di disponibilità con più istanze in più set di disponibilità.<br/><br/>Vedere il [servizio di servizio Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Applicazione di patch al sistema operativo        | Automatico  | Gestito dai clienti |
| Applicazione di patch a MySQL     | Automatico  | Gestito dai clienti |
| Disponibilità elevata | Il modello a disponibilità elevata (HA) si basa su meccanismi di failover incorporati per quando si verifica un'interruzione a livello di nodo. In questi casi, il servizio crea automaticamente una nuova istanza e collega l'archiviazione a questa istanza. | I clienti progettano, implementano, testano e mantengono la disponibilità elevata. Le funzionalità possono includere clustering, replica e così via.|
| Ridondanza della zona | Attualmente non supportati | Le macchine virtuali di Azure possono essere configurate per l'esecuzione in zone di disponibilità diverse. Per una soluzione locale, i clienti devono creare, gestire e gestire il proprio data center secondario.|
| Scenari ibridi | Con la [replica dei](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)dati , è possibile sincronizzare i dati da un server MySQL esterno nel servizio Database di Azure per MySQL. Il server esterno può trovarsi in locale, in macchine virtuali, o essere un servizio di database ospitato da altri provider di servizi cloud.<br/><br/> Con la funzionalità di replica di [lettura,](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) è possibile replicare i dati da un database di Azure per il server master MySQL a un massimo di cinque server di replica di sola lettura. Le repliche si trovano all'interno della stessa area di Azure o tra aree diverse. Le repliche di sola lettura vengono aggiornate in modo asincrono utilizzando la tecnologia di replica binlog.| Gestito dai clienti
| Backup e ripristino | Crea automaticamente [i backup del server](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) e li archivia in un archivio configurato dall'utente ridondante localmente o con ridondanza geografica. Il servizio esegue backup completi, differenziali e del log delle transazioni | Gestito dai clienti |
| Monitoraggio delle operazioni del databaseMonitoring database operations | Offre ai clienti la possibilità di [impostare avvisi](https://docs.microsoft.com/azure/mysql/concepts-monitoring) sul funzionamento del database e agire in caso di raggiungimento delle soglie. | Gestito dai clienti |
| Advanced Threat Protection | Fornisce [Advanced Threat Protection](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Questa protezione rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. | I clienti devono costruire questa protezione per se stessi.
| Ripristino di emergenza | Archivia i backup automatici in [un archivio ridondante o georidriso](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)configurato dall'utente. I backup possono anche ripristinare un server a un punto nel tempo. Il periodo di conservazione è compreso tra 7 e 35 giorni. Il ripristino viene eseguito tramite il portale di Azure.Restoration is accomplished by using the Azure portal. | Completamente gestito dai clienti. Le responsabilità includono, a dire, problemi di programmazione, test, archiviazione, archiviazione e conservazione. Un'opzione aggiuntiva consiste nell'usare un insieme di credenziali di Servizi di ripristino di Azure per eseguire il backup delle macchine virtuali e dei database di Azure nelle macchine virtuali. Questa opzione è in anteprima. |
| Raccomandazioni per le prestazioni | Fornisce ai clienti [consigli sulle prestazioni](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) basati sui file di log dei dati di utilizzo generati dal sistema. I consigli aiutano a ottimizzare i carichi di lavoro. | Gestito dai clienti |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Motivazioni aziendali per la scelta di PaaS o IaaS

Ci sono diversi fattori che possono influenzare la vostra decisione di scegliere PaaS o IaaS per ospitare i vostri database MySQL.

### <a name="cost"></a>Costi

Il finanziamento limitato è spesso la considerazione principale che determina la soluzione migliore per l'hosting dei database. Questo è vero se sei una startup con pochi soldi o un team in una società consolidata che opera sotto vincoli di budget stretti. Questa sezione descrive le nozioni di base su fatturazione e licenze in Azure applicate al database di Azure per MySQL e MySQL nelle macchine virtuali di Azure.This section describes billing and licensing basics in Azure as they apply to Azure Database for MySQL and MySQL on Azure VMs.

#### <a name="billing"></a>Fatturazione

Database di Azure per MySQL è attualmente disponibile come servizio in diversi livelli con prezzi diversi per le risorse. Tutte le risorse vengono fatturate ogni ora a tariffa fissa. Per informazioni aggiornate sui livelli di servizio, le dimensioni di calcolo e gli importi di archiviazione attualmente supportati, vedere Modello di [acquisto basato su vCore.](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) È possibile regolare dinamicamente i livelli di servizio e le dimensioni di calcolo in base alle varie esigenze di velocità effettiva dell'applicazione. Ti viene addebitato il traffico Internet in uscita a velocità di [trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)regolari.

Con il database di Azure per MySQL, Microsoft configura, patch e aggiorna automaticamente il software del database. Queste azioni automatizzate riducono i costi di amministrazione. Inoltre, il database di Azure per MySQL include funzionalità di [backup incorporate.](https://docs.microsoft.com/azure/mysql/concepts-backup) Queste funzionalità consentono di ottenere notevoli risparmi sui costi, soprattutto quando si dispone di un numero elevato di database. Al contrario, con MySQL in macchine virtuali di Azure è possibile scegliere ed eseguire qualsiasi versione di MySQL.In contrast, with MySQL on Azure VMs you can choose and run any MySQL version. Indipendentemente dalla versione di MySQL utilizzata, si paga per la macchina virtuale di cui è stato eseguito il provisioning e i costi per il tipo di licenza MySQL specifico utilizzato.

Database di Azure per MySQL offre disponibilità elevata incorporata per qualsiasi tipo di interruzione a livello di nodo pur mantenendo la garanzia del contratto di servizio del 99,99% per il servizio. Tuttavia, per la disponibilità elevata del database all'interno delle macchine virtuali, i clienti devono usare le opzioni di disponibilità elevata, ad esempio la replica MySQL disponibili in un database MySQL.However, for database high availability within VMs, customers should use the high availability options like [MySQL replication](https://dev.mysql.com/doc/refman/8.0/en/replication.html) that are available on a MySQL database. L'utilizzo di un'opzione di disponibilità elevata supportata non fornisce un servizio di controllo di accesso client aggiuntivo. Ma consente di ottenere una maggiore disponibilità del database del 99,99% a costi aggiuntivi e costi amministrativi.

Per altre informazioni sui prezzi, vedere gli articoli seguenti:For more information on pricing, see the following articles:
* [Prezzi del database di Azure per MySQLAzure Database for MySQL pricing](https://azure.microsoft.com/pricing/details/mysql/)
* [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Calcolatore prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Amministrazione

Per molte aziende, la decisione di passare a un servizio cloud riguarda tanto la complessità di offload dell'amministrazione quanto il costo. Con IaaS e PaaS, Microsoft:

- Amministra l'infrastruttura sottostante.
- Replica automaticamente tutti i dati per fornire il ripristino di emergenza.
- Configura e aggiorna il software del database.
- Gestisce il bilanciamento del carico.
- Esegue il failover trasparente se si verifica un errore del server.

Nell'elenco seguente vengono descritte le considerazioni amministrative per ogni opzione:The following list describes administrative considerations for each option:

* Con il database di Azure per MySQL, è possibile continuare ad amministrare il database. Ma non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Di seguito sono riportati alcuni esempi di elementi che è possibile continuare ad amministrare:

  - Database
  - Accesso
  - Ottimizzazione dell'indice
  - Ottimizzazione delle query
  - Controllo
  - Security

  Inoltre, la configurazione della disponibilità elevata in un altro data center richiede una configurazione o un'amministrazione minima o nulla.

* Con MySQL nelle macchine virtuali di Azure, si ha il controllo completo sul sistema operativo e sulla configurazione dell'istanza del server MySQL.With MySQL on Azure VMs, you have full control over the operating system and the MySQL server instance configuration. Con una macchina virtuale, è possibile decidere quando aggiornare o aggiornare il sistema operativo e il software di database. È inoltre possibile decidere quando installare qualsiasi software aggiuntivo, ad esempio un'applicazione antivirus. Alcune funzionalità automatizzate sono disponibili per semplificare notevolmente l'applicazione di patch, il backup e la disponibilità elevata. È possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Per altre informazioni, vedere Dimensioni delle macchine virtuali e dei servizi cloud per Azure.For more information, see [Virtual machine and cloud service sizes for Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)

### <a name="time-to-move-to-azure"></a>È il momento di passare ad Azure

* Database di Azure per MySQL è la soluzione giusta per le applicazioni progettate per il cloud quando la produttività degli sviluppatori e il time-to-market rapidi per le nuove soluzioni sono fondamentali. Con funzionalità a livello di codice che è come DBA, il servizio è adatto per gli architetti cloud e gli sviluppatori perché riduce la necessità di gestire il sistema operativo sottostante e il database.

* Quando si vuole evitare il tempo e le spese per l'acquisizione di nuovo hardware locale, MySQL nelle macchine virtuali di Azure è la soluzione giusta per le applicazioni che richiedono un database MySQL o l'accesso alle funzionalità MySQL in Windows o Linux. Questa soluzione è adatta anche per la migrazione di applicazioni e database locali esistenti in Azure intatti, per i casi in cui il database di Azure per MySQL è una soluzione scadente.

  Poiché non è necessario modificare la presentazione, l'applicazione e i livelli di dati, è possibile risparmiare tempo e budget per riprogettare la soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sull'risoluzione di alcune ottimizzazioni delle prestazioni che la piattaforma Azure potrebbe richiedere.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Prezzi del database di Azure per MySQL](https://azure.microsoft.com/pricing/details/MySQL/).
* Iniziare [creando il primo server](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).
