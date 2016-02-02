<properties
	pageTitle="Opzioni e prestazioni del database SQL: livelli di servizio | Microsoft Azure"
	description="Confrontare le prestazioni e le funzionalità di continuità aziendale dei vari livelli di servizio del database SQL per trovare il giusto compromesso tra costi e funzionalità a sostegno della scalabilità."
	keywords="opzioni di database,prestazioni del database"
	services="sql-database"
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="12/22/2015"
	ms.author="jroth"/>

# Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL


[Il database SQL di Azure](sql-database-technical-overview.md) fornisce più livelli di servizio per gestire diversi tipi di carichi di lavoro. È possibile [creare un database singolo](sql-database-get-started.md) con caratteristiche e prezzi definiti. Oppure è possibile gestire tutti i database [creando un pool di database elastici](sql-database-elastic-pool-portal.md). In entrambi i casi, tra le opzioni incluse vi sono le edizioni **Basic**, **Standard**, e **Premium**. Le caratteristiche di tali livelli, tuttavia, variano a seconda che si crei un database singolo o un database all'interno di un pool di database elastici. Questo articolo fornisce una panoramica dei livelli di servizio in entrambi i contesti.

## Livelli di servizio e opzioni di database
I livelli di servizio Basic, Standard e Premium garantiscono un tempo di attività previsto dal contratto di servizio del 99,99% e offrono prestazioni prevedibili, opzioni di continuità aziendale flessibili, funzionalità di sicurezza e fatturazione su base oraria. Nella tabella seguente sono riportati esempi dei livelli ottimali adatti ai carichi di lavoro di diverse applicazioni.

| Livello di servizio | Carichi di lavoro di destinazione |
|---|---|
| **Basic** | Più adatto a un database di piccole dimensioni, che supportano in genere un'unica operazione attiva in un determinato momento. Ad esempio, database utilizzati per lo sviluppo o a scopo di test o le applicazioni su scala ridotta utilizzate raramente. |
| **Standard** | Opzione più adatta per la maggior parte delle applicazioni cloud, che supporta più query contemporaneamente. Ad esempio, applicazioni web o per gruppi di lavoro. |
| **Premium** | Progettato per elevato volume di transazioni, che supporta un numero elevato di utenti simultanei e richiede il massimo livello di funzionalità di continuità aziendale. Ad esempio, database che supportano applicazioni mission-critical. |

>[AZURE.NOTE] Le edizioni Web e Business verranno ritirate dal commercio. Informazioni su come [aggiornare le edizioni Web e Business](sql-database-upgrade-new-service-tiers.md). Leggere la [Domande frequenti sull'interruzione](https://azure.microsoft.com/pricing/details/sql-database/web-business/) se si prevede di continuare a utilizzare le edizioni Web e Business.

### Livelli di servizio e di prestazioni per database singoli
Con più livelli di prestazioni all'interno di ciascun livello di servizio per i database singoli, è possibile scegliere il livello che meglio soddisfa le esigenze del carico di lavoro specifico. Se è necessario applicare la scalabilità verso l'alto o verso il basso, è possibile modificare facilmente i livelli del database nel portale di Azure classico senza tempi di inattività per l'applicazione. Per informazioni dettagliate, vedere [Modifica dei livelli di servizio e dei livelli di prestazioni di un database](sql-database-scale-up.md).

Le caratteristiche delle prestazioni riportate di seguito si applicano a database creati con [SQL Database V12](sql-database-v12-whats-new.md). In situazioni in cui l'hardware sottostante in Azure ospita più database SQL, il database otterrà comunque un set garantito di risorse e le caratteristiche relative alle prestazioni previste per il singolo database non saranno interessate.

[AZURE.INCLUDE [Tabella livelli di servizio database SQL](../../includes/sql-database-service-tiers-table.md)]


Per una migliore comprensione delle DTU, vedere la [sezione DTU](#understanding-dtus) in questo argomento.

>[AZURE.NOTE] Per una spiegazione dettagliata di tutte le altre righe in questa tabella livelli di servizio, vedere [Limiti e capacità a livello di servizio](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

### Livelli di servizio e di prestazioni per pool di database elastici in eDTU
Oltre a creare e a ridimensionare un database singolo, è inoltre possibile gestire più database all'interno di un [pool di database elastici](sql-database-elastic-pool.md). Tutti i database in un pool di database elastici condividono un insieme comune di risorse. Le caratteristiche delle prestazioni sono misurate dalle *unità di transazione del database elastico* (eDTU). Come con i database singoli, sono disponibili pool di database elastici in tre livelli di prestazioni: **Basic**, **Standard** e **Premium**. Per i database elastici questi tre livelli ancora definiscono i limiti delle prestazioni complessive e diverse funzionalità.

I pool di database elastici consentono a tali database di condividere e utilizzare risorse di DTU senza la necessità di assegnare un livello di prestazioni specifico ai database nel pool. Ad esempio, un singolo database in un pool Standard può passare dall’uso di 0 eDTU al valore eDTU massimo per il database (o 100 eDTU definito dal livello di servizio o un numero personalizzato configurato dall’utente). In questo modo sono disponibili più database con diversi carichi di lavoro per un utilizzo efficiente delle risorse eDTU disponibili per l'intero pool.

La tabella seguente descrive le caratteristiche dei livelli di servizio dei pool di database elastici.

[AZURE.INCLUDE [Tabella livelli di servizio di database SQL per database elastici](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Ciascun database all'interno di un pool è inoltre conforme alle caratteristiche di database singolo per il livello in questione. Ad esempio, il pool Basic ha un limite per numero massimo di sessioni per pool di 2400 – 28800, ma un singolo database all'interno di tale pool ha un limite di 300 sessioni (limite per un singolo database Basic come specificato nella sezione precedente).

### Informazioni sulle DTU

[AZURE.INCLUDE [Descrizione di DTU del database SQL](../../includes/sql-database-understanding-dtus.md)]

## Monitoraggio delle prestazioni del database
Il monitoraggio delle prestazioni di un database SQL inizia con il monitoraggio dell'uso delle risorse rispetto al livello di prestazioni scelto per il database. Questi dati rilevanti sono esposti nei modi seguenti:

1.	Il portale classico di Microsoft Azure.

2.	Viste della gestione dinamica nel database utente e nel database master del server che contiene il database utente.

Nel [portale di Azure](https://portal.azure.com/) è possibile monitorare l'utilizzo di un database singolo selezionandolo e facendo clic sul grafico di **monitoraggio**. Verrà visualizzata una finestra della **metrica** in cui è possibile apportare modifiche facendo clic su l pulsante **Modifica grafico**. Aggiungere le metriche seguenti

- Percentuale di CPU
- Percentuale di DTU
- Percentuale IO dati
- Percentuale di archiviazione

Dopo aver aggiunto queste metriche, è possibile continuare a visualizzarle nel grafico di **monitoraggio** con ulteriori dettagli sulla finestra della **metrica**. Tutte le quattro metriche mostrano la percentuale media di utilizzo relativa alla **DTU** del database.

![Monitoraggio del livello di servizio delle prestazioni del database.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

È inoltre possibile configurare gli avvisi sulle metriche delle prestazioni. Scegliere il pulsante **Aggiungi avviso** nella finestra **Metrica**. Seguire la procedura guidata per configurare l'avviso. È possibile scegliere di ricevere un avviso se la metrica supera una determinata soglia o scende al di sotto di una determinata soglia.

Ad esempio, se si prevede un aumento del carico di lavoro sul database, è possibile scegliere di configurare un avviso di posta elettronica ogni volta che il database raggiunge l'80% per una qualsiasi delle metriche delle prestazioni. È possibile utilizzarlo come un preavviso per capire quando potrebbe essere necessario passare a livello di prestazioni superiore.

Le metriche delle prestazioni consentono inoltre di determinare se è possibile effettuare il downgrade a un livello di prestazioni inferiore. Presupporre di utilizzare un database Standard S2 e tutte le metriche delle prestazioni mostrano che il database in media non utilizza più del 10% in un dato momento. È probabile che il database funzioni bene in Standard S1. Tuttavia, prestare attenzione ai picchi o alle fluttuazioni dei carichi di lavoro prima di decidere di passare a un livello di prestazioni inferiore.

Le stesse metriche esposte nel portale sono disponibili anche tramite le viste di sistema: [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) nel database **master** logico del server, e [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) nel database utente (**sys.dm\_db\_resource\_stats** creato in ciascun database utente Basic, Standard e Premium. I database edizione Web e Business restituiscono un set di risultati vuoto). Utilizzare **sys.resource\_stats** se è necessario monitorare meno dati granulari in un periodo di tempo più lungo. Utilizzare **sys.dm\_db\_resource\_stats** se è necessario monitorare più dati granulari in un periodo di tempo più breve. Per ulteriori informazioni, vedere la [Guida alle prestazioni del database SQL di Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

Per i pool di database elastici, è possibile monitorare i singoli database nel pool con le tecniche descritte in questa sezione. Tuttavia, è anche possibile monitorare il pool nel complesso. Per informazioni, vedere [Monitorare e gestire un pool di database elastici](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool).

## Passaggi successivi
Ulteriori informazioni sui prezzi di tali livelli sono disponibili in [Database SQL - Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).

Se si è interessati alla gestione di più database come gruppo, considerare i [pool di database elastici](sql-database-elastic-pool-guidance.md) insieme alle [considerazioni di prezzo e prestazioni per i pool database elastici](sql-database-elastic-pool-guidance.md).

Dopo aver acquisito familiarità con i livelli di database SQL, provare la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) per scoprire [come creare il primo database SQL](sql-database-get-started.md).

<!---HONumber=AcomDC_0128_2016-->