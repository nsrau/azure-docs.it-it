---
title: Domande frequenti sul database SQL di Azure | Microsoft Docs
description: Le risposte a domande comuni dei clienti su database cloud e Database SQL di Azure, sistema di gestione di database relazionali di Microsoft (RDBMS) e database come servizio nel cloud.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: 2e4f2bf4303d2a6f52f6dac7e8d71eca24800f53
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092070"
---
# <a name="sql-database-faq"></a>Domande frequenti sul database SQL

## <a name="what-is-the-current-version-of-sql-database"></a>Qual è la versione corrente del database SQL?
La versione corrente del database SQL è V12. La versione V11 è stata ritirata.

## <a name="what-is-the-sla-for-sql-database"></a>Qual è il contratto di servizio per il database SQL?
Microsoft garantisce che per almeno il 99,99% del tempo il cliente disponga della connettività tra il database SQL di Microsoft Azure e il gateway Internet, indipendentemente dal livello di servizio. Per altre informazioni, vedere [Contratto di servizio](http://azure.microsoft.com/support/legal/sla/).

## <a name="what-is-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Qual è il nuovo modello di acquisto basato su vCore per il database SQL di Azure?

Il nuovo modello di acquisto va ad aggiungersi al modello esistente basato su DTU. Il modello basato su vCore è stato creato per garantire ai clienti flessibilità, controllo e trasparenza, consentendo di convertire con facilità i requisiti dei carichi di lavoro locali nel cloud. Il modello consente ai clienti di ridimensionare le risorse di calcolo e di archiviazione in base alle esigenze dei carichi di lavoro. Le opzioni con database singolo e pool elastico che usano il modello vCore possono inoltre usufruire di un risparmio fino al 30% con il [Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Per altre informazioni, vedere il [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o il [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md). 

## <a name="what-is-a-vcore"></a>Che cos'è un vCore? 
Un vCore, o memoria centrale virtuale, rappresenta la CPU logica offerta con la possibilità di scegliere tra generazioni di hardware. Le CPU logiche di quarta generazione sono basate su processori Intel E5-2673 v3 (Haswell) a 2,4 GHz e le CPU logiche di quinta generazione sono basate su processori Intel E5-2673 v4 (Broadwell) a 2,3 GHz.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Il passaggio al modello basato su vCore è obbligatorio?
No, l'introduzione del modello basato su vCore nelle opzioni di distribuzione con pool elastico e database singolo rispecchia il nostro impegno a offrire ai clienti possibilità di scelta e flessibilità. Se i clienti vogliono continuare a usare il modello basato su DTU, non dovranno eseguire alcuna azione e la loro esperienza e la fatturazione non subiranno alcuna modifica. 

In molti casi le applicazioni possono sfruttare la semplicità di un bundle di risorse preconfigurato. Microsoft continua pertanto a offrire ai clienti e a supportare le opzioni basate su DTU. Se si usano le opzioni basate su DTU e queste soddisfano i requisiti aziendali, è consigliabile continuare a usarle.

I modelli basati su vCore e DTU continueranno a coesistere. Microsoft sta introducendo il modello basato su vCore in risposta alle richieste dei clienti per una maggiore trasparenza relativa alle risorse del database e per la possibilità di ridimensionare in modo separato le risorse di calcolo e archiviazione. Il modello basato su vCore consente anche risparmi aggiuntivi per i clienti con licenza Software Assurance attiva tramite il Vantaggio Azure Hybrid per SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>In base a quali criteri scegliere tra il modello di acquisto basato su DTU e quello basato su vCore? 
La DTU (Database Transaction Unit) è basata su una misura combinata di CPU, memoria, operazioni di lettura e operazioni di scrittura. I livelli di prestazioni basati su DTU rappresentano bundle preconfigurati di risorse per ottenere diversi livelli di prestazioni delle applicazioni. Il modello basato su DTU può essere più adatto alle esigenze dei clienti che non vogliono occuparsi delle risorse sottostanti e preferiscono la semplicità di un bundle preconfigurato, pagando un importo fisso mensile. Per i clienti a cui invece servono informazioni dettagliate aggiuntive sulle risorse sottostanti o che devono ridimensionarle in modo indipendente per ottenere prestazioni ottimali, il modello basato su vCore sarà la scelta ideale.  I clienti con una licenza Software Assurance attiva per SQL Server possono inoltre sfruttare i vantaggi dei loro investimenti esistenti e risparmiare fino al 30% con il [Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Le opzioni presenti in ciascun modello di acquisto offrono i vantaggi di un servizio completamente gestito, come i backup automatizzati, gli aggiornamenti software e le patch. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Che cos'è il Vantaggio Azure Hybrid per SQL Server? 
Il [Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) consente di massimizzare il valore degli investimenti correnti per la licenza e accelerare la migrazione nel cloud. Il Vantaggio Azure Hybrid per SQL Server è un vantaggio basato su Azure che consente di usare le licenze di SQL Server con Software Assurance per pagare una tariffa ridotta ("tariffa di base") per il database SQL. Il Vantaggio Azure Hybrid per SQL Server è disponibile come anteprima pubblica del modello di acquisto basato su vCore per i pool elastici e i database singoli del database SQL. È possibile applicare questo vantaggio anche se lo SKU è attivo, ma è opportuno notare che la tariffa di base viene applicata a partire dal momento della selezione nel portale di Azure. Non verranno rilasciati crediti retroattivamente.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Il Vantaggio Azure Hybrid per SQL Server include diritti di doppio uso della licenza?
Si può disporre dei diritti di doppio uso della licenza per 180 giorni, per garantire che le migrazioni vengano eseguite senza problemi. Al termine di questo periodo, la licenza di SQL Server può essere usata solo nel database SQL sul cloud e non prevede diritti di doppio uso in locale e nel cloud.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Qual è la differenza tra il Vantaggio Azure Hybrid per SQL Server e la mobilità delle licenze?
Microsoft offre oggi ai clienti di SQL Server con Software Assurance vantaggi relativi alla mobilità delle licenze che consentono di riassegnare le licenze a server condivisi di terze parti. Questi vantaggi possono essere usati in IaaS di Azure e EC2 di AWS.
Il Vantaggio Azure Hybrid per SQL Server differisce dalla mobilità delle licenze per due aspetti principali:
- Offre vantaggi economici per lo spostamento di carichi di lavoro altamente virtualizzati in Azure. I clienti di SQL EE possono ottenere 4 core in Azure nello SKU del livello Utilizzo generico per ogni core posseduto in locale per le applicazioni altamente virtualizzate. La mobilità delle licenze non offre particolari vantaggi economici per lo spostamento di carichi di lavoro virtualizzati nel cloud.
- Offre una destinazione PaaS in Azure (Istanza gestita di database SQL) altamente compatibile con SQL Server in locale

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Quali sono i diritti specifici inclusi nel Vantaggio Azure Hybrid per SQL Server?
I clienti di database SQL possono beneficiare dei diritti seguenti associati al Vantaggio Azure Hybrid per SQL Server:

|Footprint di licenza|Che cosa si ottiene con il Vantaggio Azure Hybrid per SQL Server?|
|---|---|
|Clienti di SQL Server Enterprise Edition con Software Assurance|<li>Possono pagare la tariffa di base per lo SKU del livello Utilizzo generico o del livello Business Critical</li><br><li>1 core in locale = 4 core nello SKU del livello Utilizzo generico</li><br><li>1 core in locale = 1 core nello SKU del livello Business Critical</li>|
|Clienti di SQL Server Standard Edition con Software Assurance|<li>Possono pagare la tariffa di base solo per lo SKU del livello Utilizzo generico</li><br><li>1 core in locale = 1 core nello SKU del livello Utilizzo generico</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Il modello basato su vCore è disponibile per Istanza gestita di database SQL?
[Istanza gestita](sql-database-managed-instance.md) è disponibile solo con il modello basato su vCore. Per altre informazioni, vedere anche la [pagina relativa ai prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>I costi di calcolo e archiviazione dipendono dal livello di servizio scelto?
Il costo delle risorse di calcolo riflette la capacità di calcolo totale di cui è stato eseguito il provisioning per l'applicazione. Nel livello di servizio Business Critical vengono allocate automaticamente almeno 3 repliche Always ON. Per riflettere questa allocazione aggiuntiva di risorse di calcolo, il prezzo del modello basato su vCore è circa 2,7 volte più alto nel livello Business Critical. Per lo stesso motivo, il prezzo dell'archiviazione per GB più alto nel livello Business Critical riflette l'I/O elevato e la bassa latenza dell'archiviazione SSD. Il costo delle risorse di archiviazione per i backup è tuttavia lo stesso perché in entrambi i casi viene usata una classe di archiviazione standard.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>I costi di archiviazione vengono addebitati in base alla configurazione iniziale o alle risorse usate dal database?
I vari tipi di archiviazione vengono fatturati in modo diverso. Per l'archiviazione dei dati, i costi vengono addebitati per le risorse di archiviazione totali di cui è stato eseguito il provisioning in base alle dimensioni massime del database o del pool selezionato. Il costo non cambia, a meno che il valore delle dimensioni massime non venga ridotto o aumentato. Le risorse di archiviazione dei backup sono associate ai backup automatizzati dell'istanza. L'incremento del periodo di conservazione dei backup comporta l'aumento delle risorse di archiviazione dei backup utilizzate dall'istanza. Non sono previsti addebiti aggiuntivi per le risorse di archiviazione dei backup fino al 100% delle risorse di archiviazione totali di cui è stato eseguito il provisioning per il server. Se si utilizza una quantità maggiore di risorse di archiviazione per i backup, viene applicato un addebito in base ai GB utilizzati in più ogni mese. Se ad esempio le risorse di archiviazione del database sono pari a 100 GB, si ottengono 100 GB per i backup senza costi aggiuntivi. Se tuttavia i dati di backup sono pari a 110 GB, è necessario pagare per i 10 GB aggiuntivi.

Per le risorse di archiviazione dei backup di un database singolo, l'addebito viene applicato in maniera proporzionale per le risorse di archiviazione allocate ai backup del database meno le dimensioni del database. Per le risorse di archiviazione dei backup di un pool elastico, l'addebito viene applicato in maniera proporzionale per le risorse di archiviazione allocate ai backup di tutti i database del pool meno le dimensioni massime dei dati del pool elastico. Un aumento delle dimensioni del database o del pool elastico, oppure della frequenza delle transazioni, richiederà più risorse di archiviazione e comporterà quindi un aumento dei costi di archiviazione per i backup.  Se si aumenta la dimensione massima dei dati, il nuovo valore viene sottratto dalle dimensioni delle risorse di archiviazione dei backup fatturate.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Come selezionare lo SKU corretto durante la conversione di un database esistente ai nuovi livelli di servizio? 
Per le applicazioni di database SQL esistenti che usano il modello basato su DTU, il livello di servizio Utilizzo generico è paragonabile al livello Standard. Il livello di servizio Business Critical è paragonabile al livello Premium. In entrambi i casi è consigliabile allocare almeno 1 vCore per ogni 100 DTU usate dall'applicazione nel modello basato su DTU.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>I nuovi livelli di servizio basati su vCore offrono livelli di prestazioni compatibili con tutti gli obiettivi del livello di servizio esistenti?
I nuovi livelli di servizio basati su vCore offrono opzioni con prestazioni analoghe per tutti i pool elastici e i database che usano 100 o più DTU.  In futuro verranno aggiunti altri obiettivi del livello di servizio per supportare carichi di lavoro con meno di 100 DTU.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Esistono delle differenze nelle funzionalità di database tra i livelli di servizio basati su DTU esistenti e i nuovi livelli di servizio basati su vCore? 
I nuovi livelli di servizio supportano un superset di funzionalità disponibili con le offerte correnti basate su DTU. Le funzionalità aggiuntive comprendono un set di DMV aggiuntive e altre opzioni per la configurazione delle risorse. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Se il database è basato sulla CPU e non usa molte risorse di archiviazione, è possibile aumentare le risorse di calcolo senza dover pagare per risorse di archiviazione aggiuntive?
Sì, è possibile selezionare in modo indipendente il livello delle risorse di calcolo necessarie per l'applicazione e mantenere invariate le risorse di archiviazione. Il valore minimo che può essere impostato per le risorse di archiviazione è 32 GB. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>I nuovi livelli basati su vCore supporteranno il ripristino temporizzato per 35 giorni come avviene attualmente? 
Il periodo di conservazione dei backup per il ripristino temporizzato può essere impostato tra 7 e 35 giorni. Le risorse di archiviazione dei backup verranno addebitate separatamente in base all'effettivo utilizzo, se questo supera la quantità di risorse di archiviazione pari alla dimensione massima dei dati. Nella versione di anteprima il periodo di conservazione predefinito per il ripristino temporizzato è di 7 giorni. In molti casi la dimensione massima dei dati è sufficiente per archiviare 7 giorni di backup.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Perché è consentita la selezione della generazione di hardware per il calcolo?
L'obiettivo è consentire la massima flessibilità, in modo da poter scegliere una configurazione di prestazioni che soddisfi al meglio le esigenze dell'applicazione. La tabella precedente illustra le differenze tra Gen4 e Gen5. In particolare, l'hardware Gen4 offre molta più memoria per ogni vCore. L'hardware Gen5 consente tuttavia di aumentare molto di più le risorse di calcolo. L'obiettivo di Microsoft è rendere chiare queste differenze, in modo che i clienti possano ottenere il miglior rapporto prezzo/prestazioni per l'applicazione.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>È necessario portare offline l'applicazione per eseguire la conversione da un database basato su DTU a un livello di servizio basato su vCore? 
I nuovi livelli di servizio offrono un semplice metodo di conversione online simile al processo esistente di aggiornamento dei database dal livello di servizio Standard a Premium e viceversa. Questa conversione può essere avviata tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>È possibile convertire un database da un livello di servizio basato su vCore a un livello di servizio basato su DTU? 
Sì, è possibile convertire facilmente il database a qualsiasi obiettivo di prestazioni supportato tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>È possibile eseguire l'aggiornamento o il downgrade tra i livelli di servizio Utilizzo generico e Business Critical? 
Sì, con alcune restrizioni. Lo SKU di destinazione deve soddisfare i requisiti di dimensioni massime del database o del pool elastico configurate per la distribuzione esistente. Se si usa il [Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), lo SKU del livello Business Critical è disponibile solo per i clienti con licenze Enterprise Edition. Solo i clienti che hanno eseguito la migrazione dall'ambiente locale al livello Utilizzo generico usando il Vantaggio Azure Hybrid per SQL Server con licenze Enterprise Edition possono eseguire l'aggiornamento al livello Business Critical. Per informazioni dettagliate, vedere l'articolo sui [diritti specifici inclusi nel Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Questa conversione non comporta tempi di inattività e può essere avviata tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, T-SQL o l'API REST. Vedere gli articoli per [gestire database singoli](sql-database-single-database-scale.md) e per [gestire pool elastici](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Se si usa un database Premium RS che non sarà disponibile a livello generale, è possibile aggiornarlo a un nuovo livello e ottenere un vantaggio simile come rapporto prezzo/prestazioni?
Poiché il modello basato su vCore consente il controllo indipendente sulla quantità di risorse di calcolo e archiviazione di cui è stato eseguito il provisioning, offre la possibilità di gestire in modo più efficace i costi ed è quindi un'opzione interessante per i database Premium RS. Il [Vantaggio Azure Hybrid per SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) offre inoltre uno sconto significativo quando viene usato il modello basato su vCore. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Con quale frequenza è possibile modificare le risorse per ogni pool?
Numero di volte desiderato. Vedere l'articolo per [gestire i pool elastici](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Quanto tempo è necessario per modificare il livello di servizio o il livello di prestazioni di un database singolo o per aggiungere o rimuovere un database da un pool elastico?
La modifica del livello di servizio di un database e lo spostamento da e verso un pool richiede che il database venga copiato nella piattaforma come operazione in background. A seconda delle dimensioni dei database, la modifica del livello di servizio può richiedere un periodo di tempo che va da pochi minuti ad alcune ore. In entrambi i casi, i database rimangono in linea e disponibili durante lo spostamento. Per ulteriori informazioni sulla modifica dei database singoli vedere [Modificare il livello di servizio di un database](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando è meglio usare un database singolo e quando invece è meglio usare database elastici?
In generale, i pool elastici sono progettati per un [modello di applicazione tipico di software-as-a-service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), in cui è presente un solo database per client o tenant. L'acquisto di database singoli e l'overprovisioning al fine di soddisfare una domanda variabile e i picchi di domanda per ogni database non sono spesso metodi convenienti. Per i pool, è possibile gestire le prestazioni collettive del pool e i database aumentano e diminuiscono automaticamente. Il motore intelligente di Azure consiglia un pool per i database se garantito da un modello di utilizzo. Per informazioni, vedere le [linee guida sui pool elastici](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Come viene indicato in fattura l'utilizzo del database SQL con il modello di acquisto basato su DTU?
Il database SQL viene fatturato con una tariffa oraria fissa prevedibile basata sul [modello di acquisto](sql-database-service-tiers-dtu.md). L'utilizzo effettivo è calcolato e ripartito su base oraria. È quindi possibile che nella fattura siano indicate frazioni di un'ora. Ad esempio, se un database esiste per 12 ore in un mese, nella fattura viene indicato l'utilizzo di 0,5 giorni. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Cosa succede se un database singolo è attivo per meno di un'ora o utilizza un maggiore livello di servizio per meno di un'ora?
Viene fatturata ogni ora per cui un database esiste utilizzando il livello di servizio più elevato + il livello di prestazioni applicati in quell'ora, indipendentemente dall'utilizzo o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database. 

Esempi:

* Se si crea un database di livello Basic e quindi si esegue immediatamente l'aggiornamento al livello Standard S1, sarà addebitata la tariffa Standard S1 per la prima ora.
* Se si aggiorna un database da Basic a Premium alle 22:00 e l'aggiornamento viene completato alle ore 01:35 del giorno successivo, viene addebitata la tariffa Premium a partire dalle ore 01:00. 
* Se si esegue il downgrade di un database da Premium a Basic alle 11:00 e viene completato alle 14:15, viene addebitata la tariffa Premium per il database fino alle 15:00 e successivamente viene addebitata la tariffa Basic.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Come viene indicato in fattura l'utilizzo dei pool elastici con il modello di acquisto basato su DTU?
Gli addebiti relativi ai pool elastici vengono riportati in fattura come DTU elastiche (eDTU) o come vCore con risorse di archiviazione in base agli incrementi indicati nella [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/sql-database/). Non è previsto alcun costo per database per i pool elastici. Viene fatturata ogni ora in cui un pool esiste con il livello massimo di eDTU o vCore, indipendentemente dall'utilizzo o dal fatto che il pool sia stato attivo per meno di un'ora. 

Esempi di modelli di acquisto basati su DTU:

* Se si crea un pool elastico Standard con 200 eDTU alle 11:18, con l'aggiunta di cinque database al pool viene addebitato il costo di 200 eDTU per l'intera ora a partire dalle 11:00 e per la restante parte della giornata.
* Nel giorno 2, alle ore 5:05 di mattina, il Database 1 inizia a utilizzare 50 DTU e rimane stabile per tutto il giorno. Il Database 2-5 fluttua tra le 0 e le 80 DTU. Durante il giorno vengono aggiunti altri cinque database che utilizzano DTU che variano nel corso della giornata. Il giorno 2 viene fatturato interamente a 200 DTU. 
* Il terzo giorno alle 05:00 vengono aggiunti altri 15 database. L'utilizzo dei database aumenta nel corso della giornata fino a quando si decide di aumentare le DTU per il pool da 200 a 400 alle ore 8:05 di sera. Gli addebiti a livello di 200 eDTU sono stati applicabili fino alle 8 di sera e sono aumentate fino a 400 per le quattro ore rimanenti. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Come vengono fatturati i pool elastici per il modello di acquisto basato su DTU?
I pool elastici vengono fatturati in base alle caratteristiche seguenti:

* Un pool elastico viene fatturato al momento della creazione, persino quando nel pool non sono presenti database.
* Un pool elastico viene fatturato su base oraria. Si tratta della stessa frequenza di controllo dei livelli di prestazioni dei database singoli.
* Se viene ridimensionato, un pool elastico non viene fatturato in base al nuovo numero di risorse fino al completamento dell'operazione di ridimensionamento. Tale comportamento segue lo stesso modello della modifica del livello di prestazioni dei database singoli.
* Il prezzo di un pool elastico è basato sul numero di risorse del pool. Il prezzo di un pool elastico è indipendente dal numero e dall'uso dei database elastici in esso contenuti.

Per altri dettagli, vedere [Prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/), [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) o [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Come viene indicato in fattura l'utilizzo del modello basato su vCore? 
Nel modello basato su vCore il servizio viene fatturato in base a una tariffa oraria prevedibile basata sul livello di servizio, le risorse di calcolo sottoposte a provisioning in vCore, i GB di risorse di archiviazione sottoposte a provisioning al mese e le risorse di archiviazione dei backup utilizzate. Se le risorse di archiviazione dei backup superano le dimensioni totali del database, ovvero il 100% delle dimensioni del database, verranno addebitati costi aggiuntivi. Le ore di vCore, le risorse di archiviazione dei database configurate, le operazioni di I/O utilizzate e le risorse di archiviazione dei backup sono elencate chiaramente nella fattura, facilitando così la lettura dei dettagli delle risorse usate. Il prezzo delle risorse di archiviazione dei backup fino al 100% delle dimensioni massime del database è incluso. Oltre tale valore vengono addebitati i GB utilizzati ogni mese.

Ad esempio: 
- Se il database SQL esiste per 12 ore al mese, la fattura indica un utilizzo pari a 12 ore di vCore. Se il database SQL ha eseguito il provisioning di 100 GB aggiuntivi di archiviazione, la fattura mostra l'utilizzo delle risorse di archiviazione in unità di GB/mese con ripartizione proporzionale oraria e il numero di operazioni di I/O utilizzate in un mese.
- Se il database SQL è attivo per meno di un'ora, vengono addebitate tutte le ore di esistenza del database usando il livello di servizio più elevato selezionato, le risorse di archiviazione con provisioning e le operazioni di I/O applicabili durante tale ora, indipendentemente dall'utilizzo o dal fatto che il database sia stato attivo per meno di un'ora.
- Se si crea un'istanza gestita e la si elimina cinque minuti dopo, verrà addebitata un'ora di database.
- Se si crea un'istanza gestita nel livello Utilizzo generico con 8 vCore e dopo si esegue immediatamente l'aggiornamento a 16 vCore, verrà addebitata la tariffa per 16 vCore per la prima ora.

> [!NOTE]
> Per un periodo limitato di tempo, fino al 30 giugno 2018, non vengono applicati addebiti per i backup e le operazioni di I/O.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Come viene indicato in fattura l'uso della replica geografica attiva in un pool elastico?
A differenza dei database singoli, l'uso della [replica geografica attiva](sql-database-geo-replication-overview.md) con i database elastici non ha un impatto diretto sulla fatturazione.  Vengono addebitate solo le risorse delle quali è stato eseguito il provisioning per ognuno dei pool (pool primario e secondario).

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>In che modo l'utilizzo della funzionalità di controllo influisce sulla fatturazione?
La funzionalità di controllo è integrata nel servizio del database SQL senza costi aggiuntivi ed è disponibile per tutti i livelli di servizio. Tuttavia, per archiviare i log di controllo, la funzionalità di controllo utilizza un account di archiviazione di Azure e le tariffe per le tabelle e code di archiviazione di Azure si applicano in base alle dimensioni del registro di controllo.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Come si reimposta la password per l'amministratore del server?
Nel [portale di Azure](https://portal.azure.com) fare clic su **SQL Server**, selezionare il server dall'elenco e quindi fare clic su **Reimposta password**.

## <a name="how-do-i-manage-databases-and-logins"></a>Come si gestiscono database e accessi?
Vedere [Gestione di database e accessi](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Come si può garantire che l'accesso al server sia consentito solo agli indirizzi IP autorizzati?
Vedere [Procedura: configurare le impostazioni del firewall su Database SQL mediante il portale di Azure](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Qual è l'intervallo di replica previsto durante la replica geografica di un database tra due aree della stessa area geografica di Azure?
È supportato un RPO pari a cinque secondi e l'intervallo di replica è minore se la replica geografica secondaria è ospitata nell'area associata di Azure consigliata e appartiene allo stesso livello di servizio.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Qual è l'intervallo di replica previsto quando la replica geografica secondaria viene creata nella stessa area del database primario?
In base ai dati empirici non c'è molta differenza tra l'intervallo di replica intra-area e l'intervallo inter-area quando viene usata l'area associata di Azure consigliata. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Se si verifica un errore di rete tra due aree, come funziona la logica di ripetizione quando è configurata la replica geografica?
Se si verifica una disconnessione, viene eseguito un tentativo ogni 10 secondi per ristabilire le connessioni.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Cosa si può fare per garantire che una modifica importante al database primario venga replicata?
La replica geografica secondaria è una replica asincrona per la quale non viene eseguita la sincronizzazione completa con la replica primaria. Ma viene fornito un metodo per forzare la sincronizzazione al fine di garantire la replica delle modifiche importanti (ad esempio, gli aggiornamenti della password). La sincronizzazione forzata si riflette sulle prestazioni poiché blocca il thread delle chiamante fino a quando non vengono replicate tutte le transazioni. Per informazioni dettagliate, vedere [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quali strumenti sono disponibili per monitorare l'intervallo di replica tra il database primario e la replica geografica secondaria?
L'intervallo di replica in tempo reale tra il database primario e la replica geografica secondaria è esposto attraverso una vista a gestione dinamica (DMV). Per informazioni dettagliate, vedere [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Per spostare un database in un server diverso nella stessa sottoscrizione
Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare un database dall'elenco e quindi fare clic su **Copia**. Per altre informazioni, vedere [Copiare un database SQL di Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Per spostare un database tra sottoscrizioni
Nel [portale di Azure](https://portal.azure.com)fare clic su **SQL Server** e quindi selezionare dall'elenco il server che ospita il database. Fare clic su **Sposta**, quindi selezionare le risorse da spostare e la sottoscrizione in cui spostarle.

