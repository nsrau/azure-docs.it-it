<properties 
   pageTitle="Domande frequenti su Database SQL di Azure" 
   description="Le risposte a domande comuni dei clienti su database cloud e Database SQL di Azure, sistema di gestione di database relazionali di Microsoft (RDBMS) e database come servizio nel cloud." 
   services="sql-database" 
   documentationCenter="" 
   authors="CarlRabeler" 
   manager="jhubbard" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/25/2016"
   ms.author="sashan;carlrab"/>

# Domande frequenti sul database SQL

## Come viene indicato l'utilizzo del database SQL in fattura? 
Il Database SQL emette fattura in base a una in una tariffa oraria stimabile in base al livello di servizio + il livello di prestazioni per singoli database o DTU per ogni pool di database elastici. L'utilizzo effettivo è calcolato e ripartito su base oraria. È quindi possibile che nella fattura siano indicate frazioni di un’ora. Ad esempio, se un database esiste per 12 ore in un mese, nella fattura sarà indicato l'utilizzo di 0,5 giorni. Inoltre, i livelli di servizio + i livelli delle prestazioni e le DTU per ogni pool sono suddivisi in fattura per permettere di vedere più facilmente il numero di giorni di database utilizzati per ognuno di essi in un singolo mese.

## Cosa succede se un database singolo è attivo per meno di un'ora o utilizza un maggiore livello di servizio per meno di un'ora?
Viene fatturata ogni ora per cui un database esiste utilizzando il livello di servizio più elevato + il livello di prestazioni applicati in quell'ora, indipendentemente dall'utilizzo o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato 5 minuti dopo, la fattura riporterà l'addebito relativo a 1 ora di database.

esempi
	
- Se si crea un database di livello Basic e quindi si esegue immediatamente l'aggiornamento al livello Standard S1, sarà addebitata la tariffa Standard S1 per la prima ora.

- Se si aggiorna un database da Basic a Premium alle 10:00 della sera e l'aggiornamento viene completato all’1:35 della mattina del giorno successivo, la tariffa Premium sarà addebitata solo a partire dall’1:00.

- Se si effettua il downgrade di un database Premium a Basic alle ore 11:00 di mattina e viene completato alle ore 2:15 dei pomeriggio, per il database verrà addebitata la tariffa Premium fino alle 3:00 del pomeriggio. Dopo tale ora si addebiteranno le tariffe Basic.

## Come viene indicato l'utilizzo del pool di database elastici nella fattura e cosa accade quando si modificano le DTU per ciascun pool?
Gli addebiti relativi ai pool di database elastici vengono mostrati in fattura come DTU di database elastico (eDTU) sotto agli incrementi mostrati nelle DTU per ciascun pool nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/sql-database/). Non è previsto alcun costo per ogni database per i pool di database elastici. Viene fatturata ogni ora che un pool esiste con le DTU più alte, indipendentemente dall'utilizzo o dal fatto che il pool sia stato attivo per meno di un'ora.

esempi

- Se si crea un pool di database elastici Standard con 200 DTU alle ore 11:18 di mattina, aggiungendo cinque database al pool, verranno addebitate 200 DTU per l'intera ora, a partire dalle 11 di mattina per tutto il resto del giorno.
- Nel giorno 2, alle ore 5:05 di mattina, il Database 1 inizia a utilizzare 50 DTU e rimane stabile per tutto il giorno. Il Database 2-5 fluttua tra le 0 e le 80 DTU. Durante il giorno vengono aggiunti altri cinque database che utilizzano DTU che variano nel corso della giornata. Il giorno 2 viene fatturato interamente a 200 DTU.
- Il giorno 3, alle ore 5 di mattina vengono aggiunti altri 15 database. L'utilizzo dei database aumenta nel corso della giornata fino a quando si decide di aumentare le DTU per il pool da 200 a 400 alle ore 8:05 di sera. Gli addebiti a livello di 200 DTU sono stati applicabili fino alle 8 di sera e sono aumentate fino a 400 per le 4 ore rimanenti.

## Come viene indicato l'uso della replica geografica attiva in un pool di database elastici nella fattura?
A differenza dei singoli database, l'uso della [replica geografica attiva](sql-database-geo-replication-overview.md) con i database elastici non ha un impatto diretto sulla fatturazione. Vengono addebitate solo le DTU della quali si è effettuato il provisioning per ognuno dei pool (pool primario e secondario)

## In che modo l'utilizzo della funzionalità di controllo influisce sulla fatturazione? 
La funzionalità di controllo è integrata nel servizio del database SQL, senza costi aggiuntivi, ed è disponibile per i database Basic, Standard e Premium. Tuttavia, per archiviare i log di controllo, la funzionalità di controllo utilizza un account di archiviazione di Azure e le tariffe per le tabelle e code di archiviazione di Azure si applicano in base alle dimensioni del registro di controllo.

## Come è possibile trovare i livelli di servizio e delle prestazioni corretti per i singoli database e per i pool di database elastici? 
Sono disponibili alcuni strumenti.

- Per i database locali, utilizzare la [gestione del ridimensionamento delle DTU](http://dtucalculator.azurewebsites.net/), che consiglia i database e le DTU necessarie e valuta più database per i pool di database elastici.
- Se un database singolo potrebbe trarre vantaggio nel far parte di in un pool, il motore intelligente di Azure consiglia un pool di database elastici se rileva un modello di utilizzo cronologico che garantisca tale vantaggio. Vedere [Monitorare e gestire un pool di database elastici con il portale di Azure](sql-database-elastic-pool-manage-portal.md). Vedere [Considerazioni sul prezzo e sulle prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md) per informazioni su come eseguire i calcoli matematici personalmente.
- Per vedere se è necessario aumentare o diminuire il livello di un database singolo, vedere [Indicazioni sulle prestazioni per database singoli](sql-database-performance-guidance.md).

## Con quale frequenza è possibile modificare il livello di servizio o di prestazioni di un database singolo? 
Con i database V12 è possibile modificare il livello di servizio (tra Basic, Standard e Premium) o il livello di prestazioni all'interno di un livello di servizio (ad esempio, da S1 a S2) con la frequenza che più si desidera. Per i database con le versioni precedenti, è possibile modificare il livello di prestazioni o di servizio fino a quattro volte in un periodo di 24 ore.

##Con quale frequenza è possibile modificare le DTU per un pool? 
Numero di volte desiderato.

## Quanto tempo è necessario per modificare il livello di servizio e il livello di prestazioni di un database singolo o spostare un database da e verso un pool di database elastici? 
La modifica del livello di servizio di un database e lo spostamento da e verso un pool richiede che il database venga copiato nella piattaforma come operazione in background. A seconda delle dimensioni dei database, possono essere necessari da pochi minuti a diverse ore. In entrambi i casi, i database rimangono in linea e disponibili durante lo spostamento. Per ulteriori informazioni sulla modifica dei singoli database vedere [Modificare il livello di servizio di un database](sql-database-scale-up.md).

## Quando è meglio usare un database singolo e quando invece è meglio usare database elastici? 
In generale, i pool di database elastici sono progettati per un [modello di applicazione tipico di software-as-a-service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), in cui è presente un solo database per client o tenant. L'acquisto di singoli database e l'overprovisioning al fine di soddisfare una domanda variabile e i picchi di domanda per ogni database non sono spesso metodi convenienti. Per i pool, è possibile gestire le prestazioni collettive del pool e i database aumentano e diminuiscono automaticamente.

Il motore intelligente di Azure consiglia un pool per i database, se ne rileva un modello di utilizzo che lo garantisca. Per altri dettagli, vedere [Indicazioni sui piani tariffari del database SQL](sql-database-service-tier-advisor.md). Per istruzioni dettagliate sulla scelta tra database singoli e elastici, consultare [Considerazioni su prezzi e prestazioni per i pool di database elastici](sql-database-elastic-pool-guidance.md).

## Che cosa significa avere fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning per l'archiviazione di backup? 
L'archiviazione di backup è l'archiviazione associata ai backup automatizzati dei database, usati per il [ripristino temporizzato](sql-database-recovery-using-backups.md#-point-in-time-restore) e il [ripristino geografico](sql-database-recovery-using-backups.md#geo-restore). Il database SQL di Microsoft Azure offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning per la risorsa di archiviazione di backup senza costi aggiuntivi. Ad esempio, se si usa un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, saranno disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera lo spazio di archiviazione di backup fornito, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure oppure di pagare lo spazio di archiviazione di backup aggiuntivo, che viene addebitato in base alla tariffa standard per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS, Read-Access Geographically Redundant Storage). Per altre informazioni sui costi per il servizio RA-GRS, vedere Dettagli prezzi di archiviazione.

## Se si sta passando da un livello Web/Business a nuovi livelli di servizio, cosa è necessario sapere?
I database SQL di Azure Web e Business sono stati ritirati e sostituiti dai livelli Basic, Standard, Premium ed Elastic. Sono presenti domande frequenti aggiuntive che dovrebbe aiutare in questo periodo di transizione. [Domande frequenti sul ritiro dell'edizione Web e Business](sql-database-web-business-sunset-faq.md)

## Qual è l'intervallo di replica previsto durante la replica geografica di un database tra due aree della stessa area geografica di Azure?  
È supportato un RPO pari a 5 secondi e l'intervallo di replica è minore se la replica geografica secondaria è ospitata nell'area associata di Azure consigliata e appartiene allo stesso livello di servizio.

## Qual è l'intervallo di replica previsto quando la replica geografica secondaria viene creata nella stessa area del database primario?  
In base ai dati empirici non c'è molta differenza tra l'intervallo di replica intra-area e l'intervallo inter-area quando viene usata l'area associata di Azure consigliata.

## Se si verifica un errore di rete tra due aree, come funziona la logica di ripetizione quando è impostata la replica geografica?  
Se si verifica una disconnessione, viene eseguito un tentativo ogni 10 secondi per ristabilire le connessioni.

## Cosa si può fare per garantire che una modifica importante al database primario venga replicata?
La replica geografica secondaria è una replica asincrona per la quale non viene eseguita la sincronizzazione completa con la replica primaria. È tuttavia disponibile un metodo che consente di forzare la sincronizzazione. Il metodo è progettato per garantire la replica delle modifiche importanti, ad esempio degli aggiornamenti delle password. Il metodo ha impatto sulle prestazioni poiché blocca il thread delle chiamante fino a quando non vengono replicate tutte le transazioni. Per informazioni dettagliate, vedere [sp\_wait\_for\_database\_copy\_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## Quali strumenti sono disponibili per monitorare l'intervallo di replica tra il database primario e la replica geografica secondaria?
L'intervallo di replica in tempo reale tra il database primario e la replica geografica secondaria è esposto attraverso una vista a gestione dinamica (DMV). Per informazioni dettagliate, vedere [sys.dm\_geo\_replication\_link\_status](https://msdn.microsoft.com/library/mt575504.aspx).

<!---HONumber=AcomDC_0803_2016-->