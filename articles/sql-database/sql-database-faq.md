---
title: Domande frequenti sul database SQL di Azure | Microsoft Docs
description: Le risposte a domande comuni dei clienti su database cloud e Database SQL di Azure, sistema di gestione di database relazionali di Microsoft (RDBMS) e database come servizio nel cloud.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sashan;carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 6ed02ead07c50b9a49e8868756b6f957d7b49b99
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="sql-database-faq"></a>Domande frequenti sul database SQL

## <a name="what-is-the-current-version-of-sql-database"></a>Qual è la versione corrente del database SQL?
La versione corrente del database SQL è V12. La versione V11 è stata ritirata.

## <a name="what-is-the-sla-for-sql-database"></a>Qual è il contratto di servizio per il database SQL?
Microsoft garantisce che per almeno il 99,99% del tempo i clienti avranno a disposizione la connettività tra il database SQL di Microsoft Azure Basic, Standard o Premium, singolo o elastico, e il gateway Internet. Per altre informazioni, vedere [Contratto di servizio](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Come si reimposta la password per l'amministratore del server?
Nel [portale di Azure](https://portal.azure.com) fare clic su **SQL Server**, selezionare il server dall'elenco e quindi fare clic su **Reimposta password**.

## <a name="how-do-i-manage-databases-and-logins"></a>Come si gestiscono database e accessi?
Vedere [Gestione di database e accessi](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Come si può garantire che l'accesso al server sia consentito solo agli indirizzi IP autorizzati?
Vedere [Procedura: configurare le impostazioni del firewall su Database SQL mediante il portale di Azure](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Come viene indicato l'utilizzo del database SQL in fattura?
Database SQL emette fattura in base a una tariffa oraria stimabile in base al livello di servizio + il livello di prestazioni per database singoli o eDTU per ogni pool elastico. L'utilizzo effettivo è calcolato e ripartito su base oraria. È quindi possibile che nella fattura siano indicate frazioni di un'ora. Ad esempio, se un database esiste per 12 ore in un mese, nella fattura viene indicato l'utilizzo di 0,5 giorni. Inoltre, i livelli di servizio + i livelli delle prestazioni e le DTU per ogni pool sono suddivisi in fattura per permettere di vedere più facilmente il numero di giorni di database utilizzati per ognuno di essi in un singolo mese.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Cosa succede se un database singolo è attivo per meno di un'ora o utilizza un maggiore livello di servizio per meno di un'ora?
Viene fatturata ogni ora per cui un database esiste utilizzando il livello di servizio più elevato + il livello di prestazioni applicati in quell'ora, indipendentemente dall'utilizzo o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database. 

esempi

* Se si crea un database di livello Basic e quindi si esegue immediatamente l'aggiornamento al livello Standard S1, sarà addebitata la tariffa Standard S1 per la prima ora.
* Se si aggiorna un database da Basic a Premium alle 22:00 e l'aggiornamento viene completato alle ore 01:35 del giorno successivo, viene addebitata la tariffa Premium a partire dalle ore 01:00. 
* Se si esegue il downgrade di un database da Premium a Basic alle 11:00 e viene completato alle 14:15, viene addebitata la tariffa Premium per il database fino alle 15:00 e successivamente viene addebitata la tariffa Basic.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Come viene indicato l'utilizzo del pool elastico nella fattura e cosa accade quando si modificano le eDTU per ciascun pool?
Gli addebiti relativi ai pool elastici vengono riportati in fattura come DTU elastiche (eDTU) sotto agli incrementi indicati nelle eDTU per ciascun pool nella [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/sql-database/). Non è previsto alcun costo per database per i pool elastici. Viene fatturata ogni ora che un pool esiste con le eDTU più alte, indipendentemente dall'utilizzo o dal fatto che il pool sia stato attivo per meno di un'ora. 

Esempi

* Se si crea un pool elastico Standard con 200 eDTU alle 11:18, con l'aggiunta di cinque database al pool viene addebitato il costo di 200 eDTU per l'intera ora a partire dalle 11:00 e per la restante parte della giornata.
* Nel giorno 2, alle ore 5:05 di mattina, il Database 1 inizia a utilizzare 50 DTU e rimane stabile per tutto il giorno. Il Database 2-5 fluttua tra le 0 e le 80 DTU. Durante il giorno vengono aggiunti altri cinque database che utilizzano DTU che variano nel corso della giornata. Il giorno 2 viene fatturato interamente a 200 DTU. 
* Il terzo giorno alle 05:00 vengono aggiunti altri 15 database. L'utilizzo dei database aumenta nel corso della giornata fino a quando si decide di aumentare le DTU per il pool da 200 a 400 alle ore 8:05 di sera. Gli addebiti a livello di 200 eDTU sono stati applicabili fino alle 8 di sera e sono aumentate fino a 400 per le quattro ore rimanenti. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Informazioni su prezzi e fatturazione dei pool elastici
I pool elastici vengono fatturati in base alle caratteristiche seguenti:

* Un pool elastico viene fatturato al momento della creazione, persino quando nel pool non sono presenti database.
* Un pool elastico viene fatturato su base oraria. Si tratta della stessa frequenza di controllo dei livelli di prestazioni dei database singoli.
* Se viene ridimensionato per un nuovo numero di eDTU, un pool elastico non viene fatturato in base al nuovo numero di eDTU fino al completamento dell'operazione di ridimensionamento. Tale comportamento segue lo stesso modello della modifica del livello di prestazioni dei database singoli.
* Il prezzo di un pool elastico è basato sul numero di eDTU del pool. Il prezzo di un pool elastico è indipendente dal numero e dall'uso dei database elastici in esso contenuti.
* Il prezzo di base viene calcolato da (numero di eDTU del pool) x (prezzo unitario per eDTU).

Il prezzo unitario delle eDTU per un pool elastico è superiore al prezzo unitario delle DTU per un database singolo nello stesso livello di servizio. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/). 

Per comprendere i livelli di eDTU e servizio, vedere l'articolo relativo a [opzioni e prestazioni del database SQL](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Come viene indicato in fattura l'uso della replica geografica attiva in un pool elastico?
A differenza dei database singoli, l'uso della [replica geografica attiva](sql-database-geo-replication-overview.md) con i database elastici non ha un impatto diretto sulla fatturazione.  Vengono addebitate solo le DTU della quali si è effettuato il provisioning per ognuno dei pool (pool primario e secondario)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>In che modo l'utilizzo della funzionalità di controllo influisce sulla fatturazione?
La funzionalità di controllo è integrata nel servizio del database SQL, senza costi aggiuntivi, ed è disponibile per i database Basic, Standard, Premium e Premium RS. Tuttavia, per archiviare i log di controllo, la funzionalità di controllo utilizza un account di archiviazione di Azure e le tariffe per le tabelle e code di archiviazione di Azure si applicano in base alle dimensioni del registro di controllo.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Come è possibile trovare i livelli di servizio e delle prestazioni corretti per i database singoli e per i pool elastici?
Sono disponibili alcuni strumenti. 

* Per i database locali, utilizzare la [gestione del ridimensionamento delle DTU](http://dtucalculator.azurewebsites.net/), che consiglia i database e le DTU necessarie e valuta più database per i pool elastici.
* Se un database singolo potrebbe trarre vantaggio dal far parte di un pool, il motore intelligente di Azure consiglia un pool elastico se rileva un modello di utilizzo cronologico che garantisca tale vantaggio. Vedere [Monitorare e gestire un pool elastico con il portale di Azure](sql-database-elastic-pool-manage-portal.md). Per informazioni dettagliate su come eseguire i calcoli di persona, vedere [Considerazioni sul prezzo e sulle prestazioni per un pool elastico](sql-database-elastic-pool.md).
* Per vedere se è necessario aumentare o diminuire il livello di un database singolo, vedere [Indicazioni sulle prestazioni per database singoli](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Con quale frequenza è possibile modificare il livello di servizio o di prestazioni di un database singolo?
È possibile modificare il livello di servizio (tra Basic, Standard, Premium e Premium RS) o il livello di prestazioni all'interno di un livello di servizio (ad esempio, da S1 a S2) con la frequenza che più si desidera. Per i database con le versioni precedenti, è possibile modificare il livello di prestazioni o di servizio fino a quattro volte in un periodo di 24 ore.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Con quale frequenza è possibile modificare le DTU per un pool?
Numero di volte desiderato.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Quanto tempo è necessario per modificare il livello di servizio o il livello di prestazioni di un database singolo o per aggiungere o rimuovere un database da un pool elastico?
La modifica del livello di servizio di un database e lo spostamento da e verso un pool richiede che il database venga copiato nella piattaforma come operazione in background. A seconda delle dimensioni dei database, la modifica del livello di servizio può richiedere un periodo di tempo che va da pochi minuti ad alcune ore. In entrambi i casi, i database rimangono in linea e disponibili durante lo spostamento. Per ulteriori informazioni sulla modifica dei database singoli vedere [Modificare il livello di servizio di un database](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando è meglio usare un database singolo e quando invece è meglio usare database elastici?
In generale, i pool elastici sono progettati per un [modello di applicazione tipico di software-as-a-service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), in cui è presente un solo database per client o tenant. L'acquisto di database singoli e l'overprovisioning al fine di soddisfare una domanda variabile e i picchi di domanda per ogni database non sono spesso metodi convenienti. Per i pool, è possibile gestire le prestazioni collettive del pool e i database aumentano e diminuiscono automaticamente. Il motore intelligente di Azure consiglia un pool per i database se garantito da un modello di utilizzo. Per informazioni, vedere le [linee guida sui pool elastici](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Che cosa significa avere fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning per l'archiviazione di backup?
L'archiviazione dei backup è l'archiviazione associata ai backup dei database automatizzati che vengono usati per il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) e il [ripristino geografico](sql-database-recovery-using-backups.md#geo-restore). Il database SQL di Microsoft Azure offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning per la risorsa di archiviazione di backup senza costi aggiuntivi. Ad esempio, se si usa un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, sono disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera lo spazio di archiviazione di backup fornito, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure oppure di pagare lo spazio di archiviazione di backup aggiuntivo, che viene addebitato in base alla tariffa standard per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS, Read-Access Geographically Redundant Storage). Per altre informazioni sui costi per il servizio RA-GRS, vedere Dettagli prezzi di archiviazione.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Se si sta passando da un livello Web/Business a nuovi livelli di servizio, cosa è necessario sapere?
I database SQL di Azure Web e Business sono stati ritirati e sostituiti dai livelli Basic, Standard, Premium, Premium RS ed Elastic. 

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
* Nel [portale di Azure](https://portal.azure.com) fare clic su **Database SQL**, selezionare un database dall'elenco e quindi fare clic su **Copia**. Per altre informazioni, vedere [Copiare un database SQL di Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Per spostare un database tra sottoscrizioni
* Nel [portale di Azure](https://portal.azure.com)fare clic su **SQL Server** e quindi selezionare dall'elenco il server che ospita il database. Fare clic su **Sposta**, quindi selezionare le risorse da spostare e la sottoscrizione in cui spostarle.

