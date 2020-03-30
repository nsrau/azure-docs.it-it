---
title: Pool di istanze (anteprima)Instance pools (preview)
description: Questo articolo descrive i pool di istanze del database SQL di Azure (anteprima).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c1e740fbfa4bf1e8a77a2d9d6060ab39dba7ae7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256211"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Che cosa sono i pool di istanze del database SQL (anteprima)?

I pool di istanze sono una nuova risorsa nel database SQL di Azure che offre un modo pratico ed economico per eseguire la migrazione di istanze SQL più piccole nel cloud su larga scala.

I pool di istanze consentono di eseguire il pre-provisioning di risorse di calcolo in base ai requisiti di migrazione complessivi. È quindi possibile distribuire più istanze gestite singole fino al livello di calcolo di cui è stato eseguito il pre-provisioning. Ad esempio, se esegui il pre-provisioning di 8 vCore, puoi distribuire due istanze vCore 2 e una 4 di vCore e quindi eseguire la migrazione dei database in queste istanze. Prima che i pool di istanze siano disponibili, i carichi di lavoro più piccoli e meno intensivi di lavoro devono spesso essere consolidati in un'istanza gestita più grande durante la migrazione al cloud. La necessità di eseguire la migrazione di gruppi di database in un'istanza di grandi dimensioni richiedeva in genere un'attenta pianificazione della capacità e una governance delle risorse, considerazioni aggiuntive sulla sicurezza e alcune operazioni aggiuntive di consolidamento dei dati a livello di istanza.

Inoltre, i pool di istanze supportano l'integrazione della rete virtuale nativa in modo da poter distribuire più pool di istanze e più istanze singole nella stessa subnet.


## <a name="key-capabilities-of-instance-pools"></a>Funzionalità chiave dei pool di istanzeKey capabilities of instance pools

I pool di istanze offrono i vantaggi seguenti:Instance pools provide the following benefits:

1. Possibilità di ospitare 2 istanze vCore. *Solo per le istanze nei pool di istanze . \**
2. Tempo di distribuzione dell'istanza prevedibile e veloce (fino a 5 minuti).
3. Allocazione minima degli indirizzi IP.

Il diagramma seguente illustra un pool di istanze con più istanze distribuite all'interno di una subnet di rete virtuale.

![pool di istanze con più istanze](./media/sql-database-instance-pools/instance-pools1.png)

I pool di istanze consentono la distribuzione di più istanze nella stessa macchina virtuale in cui le dimensioni di calcolo della macchina virtuale si basano sul numero totale di vCore allocati per il pool. Questa architettura consente il *partizionamento* della macchina virtuale in più istanze, che possono essere di qualsiasi dimensione supportata, tra cui 2 vCore (2 istanze vCore sono disponibili solo per le istanze nei pool).

Le operazioni di gestione sulle istanze di un pool sono molto più veloci dopo la distribuzione iniziale del pool. Queste operazioni sono più veloci perché la distribuzione o l'estensione di un [cluster virtuale](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (set dedicato di macchine virtuali) non fa parte del provisioning dell'istanza gestita.

Poiché tutte le istanze in un pool condividono la stessa macchina virtuale, l'allocazione IP totale non dipende dal numero di istanze distribuite, utile per la distribuzione in subnet con un intervallo di indirizzi IP ridotto.

Ogni pool dispone di un'allocazione IP fissa di soli nove indirizzi IP (esclusi i cinque indirizzi IP nella subnet riservati per le proprie esigenze). Per informazioni dettagliate, vedere Requisiti relativi [alle dimensioni delle subnet per le istanze singole.](sql-database-managed-instance-determine-size-vnet-subnet.md)

## <a name="application-scenarios-for-instance-pools"></a>Scenari di applicazione per i pool di istanzeApplication scenarios for instance pools

Nell'elenco seguente vengono forniti i principali casi d'uso in cui devono essere considerati i pool di istanze:The following list provides the main use cases where instance pools should be considered:

- Migrazione di *un gruppo di istanze SQL* contemporaneamente, in cui la maggior parte è una dimensione inferiore (ad esempio 2 o 4 vCore).
- Scenari in cui la creazione o il ridimensionamento di *istanze prevedibili e brevi* è importante. Ad esempio, la distribuzione di un nuovo tenant in un ambiente di applicazione SaaS multi-tenant che richiede funzionalità a livello di istanza.
- Scenari in cui è importante avere un *costo fisso* o un limite di *spesa.* Ad esempio, l'esecuzione di ambienti di sviluppo-test condivisi o demo di dimensioni fisse (o che cambiano raramente), in cui si distribuiscono periodicamente le istanze gestite quando necessario.
- Scenari in cui è importante *l'allocazione minima* degli indirizzi IP in una subnet della rete virtuale. Tutte le istanze di un pool condividono una macchina virtuale, pertanto il numero di indirizzi IP allocati è inferiore rispetto a quello delle singole istanze.


## <a name="architecture-of-instance-pools"></a>Architettura dei pool di istanze

I pool di istanze hanno un'architettura simile alle istanze gestite normali *(istanze singole*). Per supportare [le distribuzioni all'interno](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) di reti virtuali di Azure e per fornire isolamento e sicurezza per i clienti, i pool di istanze si basano anche su [cluster virtuali.](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) I cluster virtuali rappresentano un set dedicato di macchine virtuali isolate distribuite all'interno della subnet di rete virtuale del cliente.

La differenza principale tra i due modelli di distribuzione consiste nel fatto che i pool di istanze consentono più distribuzioni di processi di SQL Server nello stesso nodo della macchina virtuale, che sono risorse gestite tramite oggetti processo di [Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), mentre le singole istanze sono sempre sole in un nodo di macchina virtuale.

Il diagramma seguente mostra un pool di istanze e due singole istanze distribuite nella stessa subnet e illustra i principali dettagli architetturali per entrambi i modelli di distribuzione:The following diagram shows an instance pool and two individual instances deployed in the same subnet and illustrates the main architectural details for both deployment models:

![pool di istanze e due singole istanze](./media/sql-database-instance-pools/instance-pools2.png)

Ogni pool di istanze crea un cluster virtuale separato sotto. Le istanze all'interno di un pool e le singole istanze distribuite nella stessa subnet non condividono le risorse di calcolo allocate ai processi di SQL ServerSQL Server e ai componenti gateway, in modo da garantire la prevedibilità delle prestazioni.

## <a name="instance-pools-resource-limitations"></a>Limitazioni delle risorse dei pool di istanze

Esistono diverse limitazioni delle risorse relative ai pool di istanze e alle istanze contenute all'interno dei pool:

- I pool di istanze sono disponibili solo su hardware Gen5.
- Le istanze all'interno di un pool hanno CPU e RAM dedicate, pertanto il numero aggregato di vCore in tutte le istanze deve essere minore o uguale al numero di vCore allocati al pool.
- Tutti i [limiti a livello](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) di istanza si applicano alle istanze create all'interno di un pool.
- Oltre ai limiti a livello di istanza, sono previsti anche due limiti a livello di *pool di istanze:*
  - Dimensioni totali dello spazio di archiviazione per pool (8 TB).
  - Numero totale di database per pool (100).

L'allocazione totale dello storage e il numero di database in tutte le istanze devono essere inferiori o uguali ai limiti esposti dai pool di istanze.

- I pool di istanze supportano 8, 16, 24, 32, 40, 64 e 80 vCore.
- Le istanze gestite all'interno dei pool supportano 2, 4, 8, 16, 24, 32, 40, 64 e 80 vCore.
- Le istanze gestite all'interno dei pool supportano dimensioni di archiviazione comprese tra 32 GB e 8 TB, ad eccezione di:
  - 2 istanze vCore supportano dimensioni comprese tra 32 GB e 640 GB
  - 4 istanze vCore supportano dimensioni comprese tra 32 GB e 2 TB

La proprietà del [livello di servizio](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) è associata alla risorsa del pool di istanze, pertanto tutte le istanze in un pool devono essere lo stesso livello di servizio del livello di servizio del pool. Al momento, è disponibile solo il livello di servizio Scopo generale (vedere la sezione seguente sulle limitazioni nell'anteprima corrente).

### <a name="public-preview-limitations"></a>Limiti dell'anteprima pubblica

L'anteprima pubblica presenta le seguenti limitazioni:

- Attualmente è disponibile solo il livello di servizio Scopo generale.
- I pool di istanze non possono essere ridimensionati durante l'anteprima pubblica, quindi è importante un'attenta pianificazione della capacità prima della distribuzione.
- Il supporto del portale di Azure per la creazione e la configurazione del pool di istanze non è ancora disponibile. Tutte le operazioni sui pool di istanze sono supportate solo tramite PowerShell.All operations on instance pools are supported through PowerShell only. La distribuzione iniziale dell'istanza in un pool creato in precedenizzati è supportata solo tramite PowerShell.Initial instance deployment in a pre-created pool is also supported through PowerShell only. Una volta distribuite in un pool, le istanze gestite possono essere aggiornate tramite il portale di Azure.Once deployed into a pool, managed instances can be update using the Azure portal.
- Le istanze gestite create all'esterno del pool non possono essere spostate in un pool esistente e le istanze create all'interno di un pool non possono essere spostate all'esterno come istanza singola o in un altro pool.
- I prezzi delle istanze riservate (licenza inclusa o con il vantaggio Azure Hybrid) non sono disponibili.

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate

Le istanze create nei pool supportano gli stessi livelli di [compatibilità e le stesse funzionalità supportate in singole istanze gestite.](sql-database-managed-instance.md#sql-features-supported)

Ogni istanza gestita distribuita in un pool dispone di un'istanza separata di SQL Agent.Every managed instance deployed in a pool has a separate instance of SQL Agent.

Le funzionalità facoltative o quelle che richiedono la scelta di valori specifici, ad esempio regole di confronto a livello di istanza, fuso orario, endpoint pubblico per il traffico dati, gruppi di failover, sono configurate a livello di istanza e possono essere diverse per ogni istanza di un pool.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Anche se le istanze gestite all'interno dei pool dispongono di vCore e RAM dedicati, condividono il disco locale (per l'utilizzo di tempdb) e le risorse di rete. Non è probabile, ma è possibile sperimentare l'effetto *vicino rumoroso* se più istanze nel pool hanno un elevato consumo di risorse allo stesso tempo. Se si osserva questo comportamento, è consigliabile distribuire queste istanze in un pool di dimensioni maggiori o come istanze singole.

## <a name="security-considerations"></a>Considerazioni sulla sicurezza

Poiché le istanze distribuite in un pool condividono la stessa macchina virtuale, è consigliabile disabilitare le funzionalità che introducono rischi di sicurezza più elevati o controllare saldamente le autorizzazioni di accesso a queste funzionalità. Ad esempio, integrazione con CLR, backup e ripristino nativi, posta elettronica del database e così via.

## <a name="instance-pool-support-requests"></a>Richieste di supporto del pool di istanze

Creare e gestire le richieste di supporto per i pool di istanze nel [portale di Azure.](https://portal.azure.com)

Se si verificano problemi relativi alla distribuzione del pool di istanze (creazione o eliminazione), assicurarsi di specificare **Pool di** istanze nel campo **sottotipo di problema.**

![richiesta di supporto dei pool di istanze](./media/sql-database-instance-pools/support-request.png)

Se si verificano problemi relativi a singole istanze o database all'interno di un pool, è necessario creare un ticket di supporto regolare per le istanze gestite del database SQL di Azure.If you are experiencing issues related to single instances or databases within a pool, you should create a regular support ticket for Azure SQL Database managed instances.

Per creare distribuzioni di istanze gestite di grandi dimensioni (con o senza pool di istanze), potrebbe essere necessario ottenere una quota regionale maggiore. Per altre informazioni, vedere Aumenti delle quote di richiesta per il database SQL di Azure.For more information, see [Request quota increases for Azure SQL Database](quota-increase-request.md). Si noti che se si usano pool di istanze, la logica di distribuzione confronta *l'utilizzo* totale di vCore a livello di pool con la quota per determinare se è consentito creare nuove risorse senza aumentare ulteriormente la quota.

## <a name="instance-pool-billing"></a>Fatturazione pool di istanze

I pool di istanze consentono il calcolo e l'archiviazione delle dimensioni in modo indipendente. I clienti pagano per l'elaborazione associata alla risorsa del pool misurata in vCore e allo spazio di archiviazione associato a ogni istanza misurata in gigabyte (i primi 32 GB sono gratuiti per ogni istanza).

Il prezzo di vCore per un pool viene addebitato indipendentemente dal numero di istanze distribuite in tale pool.

Per il prezzo di calcolo (misurato in vCore), sono disponibili due opzioni di prezzo:

  1. *Licenza inclusa*: Il prezzo delle licenze SQL è incluso. Questo vale per i clienti che scelgono di non applicare le licenze di SQL Server esistenti con Software Assurance.
  2. *Vantaggio Azure Hybrid*: un prezzo ridotto che include il vantaggio Azure Hybrid per SQL Server.Azure Hybrid benefit : A reduced price that includes Azure Hybrid Benefit for SQL Server. I clienti possono optare per questo prezzo utilizzando le licenze di SQL Server esistenti con Software Assurance. Per l'idoneità e altri dettagli, vedere [Vantaggi ibridi](https://azure.microsoft.com/pricing/hybrid-benefit/)di Azure.For eligibility and other details, see Azure Hybrid Benefit .

L'impostazione di opzioni di prezzo diverse non è possibile per le singole istanze in un pool. Tutte le istanze nel pool padre devono essere al prezzo di licenza inclusa o al prezzo del vantaggio Azure Hybrid.All instances in the parent pool must be either at License Included price or Azure Hybrid Benefit price. Il modello di licenza per il pool può essere modificato dopo la creazione del pool.

> [!IMPORTANT]
> Se si specifica un modello di licenza per l'istanza diversa da quella del pool, viene utilizzato il prezzo del pool e il valore a livello di istanza viene ignorato.

Se si creano pool di istanze in sottoscrizioni idonee per il [vantaggio di sviluppo-test,](https://azure.microsoft.com/pricing/dev-test/)si ricevono automaticamente tariffe scontate fino al 55% nell'istanza gestita SQL di Azure.If you create instance pools on subscriptions eligible for dev-test benefit , you receive automatically discounted rates of up to 55 percent on Azure SQL managed instance.

Per informazioni dettagliate sui prezzi del pool di istanze, fare riferimento alla sezione Pool di *istanze* nella pagina dei prezzi [dell'istanza gestita.](https://azure.microsoft.com/pricing/details/sql-database/managed/)

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare i pool di istanze, vedere Guida alle procedure relative ai pool di [istanze di database SQL.](sql-database-instance-pools-how-to.md)
- Per informazioni su come creare la prima istanza gestita, vedere [Guida introduttiva](sql-database-managed-instance-get-started.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Per una guida introduttiva che crea un'istanza gestita e ripristina un database da un file di backup, vedere [creare un'istanza gestita.](sql-database-managed-instance-get-started.md)
- Per un'esercitazione sull'uso del servizio di migrazione del database di Azure per la migrazione, vedere Migrazione di [istanze gestite tramite DMS.](../dms/tutorial-sql-server-to-managed-instance.md)
- Per un monitoraggio avanzato delle prestazioni del database delle istanze gestite con la funzionalità di informazioni per la risoluzione dei problemi incorporata, vedere Monitorare il database SQL di Azure con Azure SQL Analytics.For advanced monitoring of managed instance database performance with built-in troubleshooting intelligence, see [Monitor Azure SQL Database using Azure SQL Analytics.](../azure-monitor/insights/azure-sql.md)
- Per informazioni sui prezzi, vedere Determinazione dei prezzi delle [istanze gestite dal database SQL.](https://azure.microsoft.com/pricing/details/sql-database/managed/)
