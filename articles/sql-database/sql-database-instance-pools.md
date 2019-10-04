---
title: Pool di istanze del database SQL di Azure (anteprima) | Microsoft Docs
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
ms.openlocfilehash: 34e779f04f59b23733c6fbfa3450931fccb442b1
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294256"
---
# <a name="what-are-sql-database-instance-pools-preview"></a>Che cosa sono i pool di istanze di database SQL (anteprima)?

I pool di istanze sono una nuova risorsa nel database SQL di Azure che offre un modo pratico ed economico per eseguire la migrazione di istanze SQL più piccole al cloud su larga scala.

I pool di istanze consentono di eseguire il pre-provisioning di risorse di calcolo in base ai requisiti di migrazione totali. È quindi possibile distribuire più istanze gestite singole fino al livello di calcolo di cui è stato eseguito il pre-provisioning. Se, ad esempio, si esegue il pre-provisioning di 8 Vcore, è possibile distribuire due istanze vCore e una 4 vCore e quindi eseguire la migrazione dei database in queste istanze. Prima che i pool di istanze fossero disponibili, i carichi di lavoro a elevato utilizzo di calcolo e minori dovrebbero spesso essere consolidati in un'istanza gestita più ampia durante la migrazione al cloud. La necessità di eseguire la migrazione di gruppi di database a un'istanza di grandi dimensioni richiede in genere un'attenta pianificazione della capacità e governance delle risorse, considerazioni aggiuntive sulla sicurezza e alcune operazioni di consolidamento dei dati aggiuntive a livello di istanza.

Inoltre, i pool di istanze supportano l'integrazione VNet nativa, in modo da poter distribuire più pool di istanze e più istanze singole nella stessa subnet.


## <a name="key-capabilities-of-instance-pools"></a>Funzionalità principali dei pool di istanze

I pool di istanze offrono i vantaggi seguenti:

1. Possibilità di ospitare 2 istanze vCore. *Solo per le istanze nei pool di istanze. \**
2. Tempo di distribuzione dell'istanza prevedibile e veloce (fino a 5 minuti).
3. Allocazione minima degli indirizzi IP.

Il diagramma seguente illustra un pool di istanze con più istanze distribuite in una subnet di rete virtuale.

![pool di istanze con più istanze](./media/sql-database-instance-pools/instance-pools1.png)

I pool di istanze consentono la distribuzione di più istanze nella stessa macchina virtuale in cui la dimensione di calcolo della macchina virtuale è basata sul numero totale di Vcore allocati per il pool. Questa architettura consente il *partizionamento* della macchina virtuale in più istanze, che possono essere di qualsiasi dimensione supportata, incluse 2 Vcore (2 istanze di vCore sono disponibili solo per le istanze nei pool).

Le operazioni di gestione sulle istanze in un pool sono molto più veloci dopo la distribuzione iniziale del pool. Queste operazioni sono più veloci perché la distribuzione o l'estensione di un [cluster virtuale](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture) (set dedicato di macchine virtuali) non fa parte del provisioning dell'istanza gestita.

Poiché tutte le istanze in un pool condividono la stessa macchina virtuale, l'allocazione IP totale non dipende dal numero di istanze distribuite, che risulta utile per la distribuzione in subnet con un intervallo IP limitato.

Ogni pool dispone di un'allocazione IP fissa di soli nove indirizzi IP (esclusi i cinque indirizzi IP nella subnet riservati per le proprie esigenze). Per informazioni dettagliate, vedere [requisiti delle dimensioni delle subnet per le singole istanze](sql-database-managed-instance-determine-size-vnet-subnet.md).

## <a name="application-scenarios-for-instance-pools"></a>Scenari applicativi per pool di istanze

Nell'elenco seguente sono riportati i principali casi d'uso in cui devono essere presi in considerazione i pool di istanze:

- Migrazione di *un gruppo di istanze di SQL* allo stesso tempo, in cui la maggior parte è di dimensioni minori, ad esempio 2 o 4 vcore.
- Scenari in cui la *creazione o il ridimensionamento di istanze prevedibili e brevi* è importante. Ad esempio, la distribuzione di un nuovo tenant in un ambiente di applicazioni SaaS multi-tenant che richiede funzionalità a livello di istanza.
- Gli scenari in cui è importante un *costo fisso* o un *limite di spesa* . Ad esempio, l'esecuzione di ambienti di sviluppo/test o dimostrativi condivisi di una dimensione fissa (o raramente modificata), in cui si distribuiscono periodicamente istanze gestite quando necessario.
- Scenari in cui l' *allocazione minima degli indirizzi IP* in una subnet VNet è importante. Tutte le istanze in un pool condividono una macchina virtuale, pertanto il numero di indirizzi IP allocati è inferiore rispetto al caso di istanze singole.


## <a name="architecture-of-instance-pools"></a>Architettura dei pool di istanze

I pool di istanze hanno un'architettura simile alle istanze gestite regolari (*istanze singole*). Per supportare le [distribuzioni nelle reti virtuali di Azure (reti virtuali)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) e per garantire l'isolamento e la sicurezza per i clienti, i pool di istanze si basano anche sui [cluster virtuali](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture). I cluster virtuali rappresentano un set dedicato di macchine virtuali isolate distribuite all'interno della subnet della rete virtuale del cliente.

La differenza principale tra i due modelli di distribuzione è che i pool di istanze consentono più distribuzioni di processi di SQL Server nello stesso nodo della macchina virtuale, che sono regolate dalle risorse con [gli oggetti processo di Windows](https://docs.microsoft.com/windows/desktop/ProcThread/job-objects), mentre le singole istanze sono sempre da sole un nodo della macchina virtuale.

Il diagramma seguente mostra un pool di istanze e due istanze singole distribuite nella stessa subnet e illustra i principali dettagli dell'architettura per entrambi i modelli di distribuzione:

![pool di istanze e due singole istanze](./media/sql-database-instance-pools/instance-pools2.png)

Ogni pool di istanze crea un cluster virtuale separato sotto. Le istanze in un pool e le istanze singole distribuite nella stessa subnet non condividono le risorse di calcolo allocate a SQL Server i processi e i componenti del gateway, garantendo la prevedibilità delle prestazioni.

## <a name="instance-pools-resource-limitations"></a>Limitazioni delle risorse del pool di istanze

Esistono diverse limitazioni delle risorse relative ai pool di istanze e alle istanze all'interno di pool:

- I pool di istanze sono disponibili solo nell'hardware quinta generazione.
- Le istanze in un pool hanno CPU e RAM dedicate, quindi il numero aggregato di Vcore in tutte le istanze deve essere minore o uguale al numero di Vcore allocato al pool.
- Tutti i [limiti a livello di istanza](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) si applicano alle istanze create all'interno di un pool.
- Oltre ai limiti a livello di istanza sono stati imposti anche due limiti *a livello di pool di istanze*:
  - Dimensioni totali dello spazio di archiviazione per pool (8 TB).
  - Numero totale di database per pool (100).

L'allocazione totale dello spazio di archiviazione e il numero di database in tutte le istanze devono essere inferiori o uguali ai limiti esposti dai pool di istanze.

- I pool di istanze supportano 8, 16, 24, 32, 40, 64 e 80 vcore.
- Le istanze gestite all'interno di pool supportano 2, 4, 8, 16, 24, 32, 40, 64 e 80 vcore.
- Le istanze gestite all'interno dei pool supportano dimensioni di archiviazione comprese tra 32 GB e 8 TB, tranne:
  - 2 le istanze di vCore supportano dimensioni comprese tra 32 GB e 640 GB
  - 4 le istanze di vCore supportano dimensioni comprese tra 32 GB e 2 TB

La [proprietà del livello di servizio](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) è associata alla risorsa del pool di istanze in modo che tutte le istanze in un pool siano dello stesso livello di servizio del livello di servizio del pool. Al momento, è disponibile solo il livello di servizio per utilizzo generico (vedere la sezione seguente sulle limitazioni nell'anteprima corrente).

### <a name="public-preview-limitations"></a>Limiti dell'anteprima pubblica

L'anteprima pubblica presenta le limitazioni seguenti:

- Attualmente, è disponibile solo il livello di servizio per utilizzo generico.
- Non è possibile ridimensionare i pool di istanze durante l'anteprima pubblica in modo da garantire un'attenta pianificazione della capacità prima della distribuzione.
- Il supporto portale di Azure per la creazione e la configurazione del pool di istanze non è ancora disponibile. Tutte le operazioni sui pool di istanze sono supportate solo tramite PowerShell. La distribuzione iniziale dell'istanza in un pool creato in precedenza è supportata anche solo tramite PowerShell. Una volta distribuite in un pool, è possibile aggiornare le istanze gestite usando il portale di Azure.
- Le istanze gestite create all'esterno del pool non possono essere spostate in un pool esistente e le istanze create all'interno di un pool non possono essere spostate all'esterno come una singola istanza o in un altro pool.
- I prezzi delle istanze riservate (licenza inclusa o con Vantaggio Azure Hybrid) non sono disponibili.

## <a name="sql-features-supported"></a>Funzionalità di SQL supportate

Le istanze create nei pool supportano gli stessi [livelli di compatibilità e le stesse funzionalità supportate nelle singole istanze gestite](sql-database-managed-instance.md#sql-features-supported).

Ogni istanza gestita distribuita in un pool dispone di un'istanza separata di SQL Agent.

Funzionalità o funzionalità facoltative che richiedono la scelta di valori specifici, ad esempio regole di confronto a livello di istanza, fuso orario, endpoint pubblico per il traffico dati, gruppi di failover, vengono configurati a livello di istanza e possono essere diversi per ogni istanza in un pool.

## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Sebbene le istanze gestite all'interno dei pool dispongano di vCore e RAM dedicate, condividono il disco locale (per l'utilizzo di tempdb) e le risorse di rete. Non è probabile, ma è possibile che si verifichi il fastidioso effetto *adiacente* se più istanze nel pool hanno un consumo di risorse elevato allo stesso tempo. Se si osserva questo comportamento, è consigliabile distribuire queste istanze in un pool più grande o come istanze singole.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Poiché le istanze distribuite in un pool condividono la stessa macchina virtuale, è opportuno considerare la possibilità di disabilitare le funzionalità che introducono rischi di sicurezza più elevati o di controllare con sicurezza le autorizzazioni di accesso a queste funzionalità. Ad esempio, l'integrazione con CLR, il backup e il ripristino nativi, la posta elettronica del database e così via.

## <a name="instance-pool-support-requests"></a>Richieste di supporto pool di istanze

Creare e gestire le richieste di supporto per i pool di istanze nel [portale di Azure](https://portal.azure.com).

Se si verificano problemi relativi alla distribuzione del pool di istanze (creazione o eliminazione), assicurarsi di specificare i **pool di istanze** nel campo **sottotipo problema** .

![richiesta di supporto pool di istanze](./media/sql-database-instance-pools/support-request.png)

Se si verificano problemi relativi a istanze o database singoli all'interno di un pool, è necessario creare un ticket di supporto regolare per le istanze gestite del database SQL di Azure.

Per creare distribuzioni di istanze gestite di dimensioni maggiori (con o senza pool di istanze), potrebbe essere necessario ottenere una quota regionale più ampia. Usare la [procedura standard di istanza gestita per richiedere una quota più grande](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance), ma si noti che se si usano pool di istanze, la logica di distribuzione confronta il consumo totale di vCore *a livello di pool* rispetto alla quota per determinare se si è consentito creare nuove risorse senza aumentare ulteriormente la quota.

## <a name="instance-pool-billing"></a>Fatturazione pool di istanze

I pool di istanze consentono di ridimensionare le risorse di calcolo e archiviazione in modo indipendente. I clienti pagano per il calcolo associato alla risorsa del pool misurata in Vcore e l'archiviazione associata a ogni istanza misurata in gigabyte (i primi 32 GB sono gratuiti per ogni istanza).

il prezzo vCore per un pool viene addebitato indipendentemente dal numero di istanze distribuite nel pool.

Per il prezzo di calcolo (misurato in VCore), sono disponibili due opzioni di prezzo:

  1. *Licenza inclusa*: Applicare licenze di SQL Server esistenti con Software Assurance.
  2. *Vantaggio Azure Hybrid*: Prezzo ridotto che include Vantaggio Azure Hybrid per SQL Server. I clienti possono optare per questo prezzo usando le licenze di SQL Server esistenti con Software Assurance. Per informazioni sull'idoneità e altri dettagli, vedere [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

Non è possibile impostare opzioni di prezzo diverse per le singole istanze in un pool. Tutte le istanze nel pool padre devono essere al prezzo incluso per la licenza o Vantaggio Azure Hybrid prezzo. Il modello di licenza per il pool può essere modificato dopo la creazione del pool.

> [!IMPORTANT]
> Se si specifica un modello di licenza per l'istanza diversa da nel pool, viene utilizzato il prezzo del pool e il valore del livello di istanza viene ignorato.

Se si creano pool di istanze per le [sottoscrizioni idonee per il vantaggio dev-test](https://azure.microsoft.com/pricing/dev-test/), si ricevono automaticamente tariffe scontate fino al 55% sull'istanza gestita di SQL di Azure.

Per informazioni dettagliate sui prezzi dei pool di istanze, vedere la sezione *pool di istanze* nella pagina dei [prezzi dell'istanza gestita](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare i pool di istanze, vedere [Guida alle procedure per i pool di istanze del database SQL](sql-database-instance-pools-how-to.md).
- Per informazioni su come creare la prima istanza gestita, vedere [Guida di avvio rapido](sql-database-managed-instance-get-started.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](sql-database-features.md).
- Per altre informazioni sulla configurazione della rete virtuale, vedere l'articolo relativo alla [configurazione di una rete virtuale per l'istanza gestita](sql-database-managed-instance-connectivity-architecture.md).
- Per una guida di avvio rapido sulla creazione di un'istanza gestita e il ripristino di un database da un file di backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sull'uso di Servizio Migrazione del database di Azure per la migrazione, vedere l'articolo relativo alla [migrazione all'istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).
- Per il monitoraggio avanzato delle prestazioni del database dell'istanza gestita con l'Intelligence per la risoluzione dei problemi incorporata, vedere [monitorare il database SQL di Azure con analisi SQL di Azure](../azure-monitor/insights/azure-sql.md).
- Per informazioni sui prezzi, vedere [Prezzi dell'istanza gestita di database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).