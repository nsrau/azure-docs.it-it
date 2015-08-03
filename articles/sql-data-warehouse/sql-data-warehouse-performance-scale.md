<properties
   pageTitle="Scalabilità e prestazioni elastiche con SQL Data Warehouse | Microsoft Azure"
   description="Comprendere l'elasticità di SQL Data Warehouse mediante l'uso di unità data warehouse per aumentare o ridurre le risorse di calcolo. Esempi di codice forniti."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="nicw;JRJ@BigBangData.co.uk;mausher"/>

# Scalabilità e prestazioni elastiche con SQL Data Warehouse
Per aumentare o ridurre in modo elastico la potenza di calcolo di cui si dispone, è sufficiente modificare il numero delle unità data warehouse (DWU) allocate alla propria istanza di SQL Data Warehouse. Le unità data warehouse sono un nuovo concetto introdotto con SQL Data Warehouse per consentirne una gestione semplice ed efficace. Questo argomento è un'introduzione alle unità data warehouse in cui viene spiegato come usarle per scalare in modo elastico la potenza di calcolo. L'articolo fornisce anche alcune indicazioni iniziali su come impostare un valore DWU ragionevole per l'ambiente in uso.

## Che cos'è un'unità data warehouse?
Microsoft di fondo esegue diversi test benchmark delle prestazioni per stabilire quanto hardware e quale configurazione consentiranno di fornire un'offerta competitiva ai clienti. Il calcolo può essere aumentato o ridotto in blocchi di 100 DWU, ma non tutti i multipli di 100 DWU sono disponibili.

## Quante DWU è consigliabile usare?
SQL Data Warehouse può rendere accessibili molte soluzioni diverse per i clienti. È pertanto disponibile una vasta gamma di tipi di query che i clienti eseguiranno e di volumi di dati su cui opereranno, così come saranno varie l'architettura dello schema, la modalità di distribuzione dei dati, la quantità di utenti che accederanno ai dati, la frequenza di accesso e così via.

Invece di fornire per le DWU punti di partenza vincolanti che possano essere appropriati per una categoria di clienti, la questione viene affrontata con un approccio pratico. Le prestazioni in SQL Data Warehouse vengono scalate in modo lineare e il passaggio da una scala di calcolo a un'altra (ad esempio, da 100 DWU a 2000 DWU) avviene nel giro di pochi secondi. Questo consente di provare liberamente le diverse impostazioni fino a individuare quella più adatta per il proprio scenario.


1. Per un data warehouse in sviluppo, iniziare con un numero limitato di DWU.
2. Monitorare le prestazioni dell'applicazione, in modo da poter acquisire familiarità con le DWU in relazione alle prestazioni osservate.
3. Determinare di quanto si vogliono aumentare o ridurre le prestazioni per raggiungere il livello ottimale per i propri requisiti aziendali presupponendo una scalabilità lineare. 
4. Modificare il numero di DWU in uso aumentandone o riducendone la quantità in base alle esigenze. Il servizio risponderà rapidamente per adattare le risorse di calcolo in modo che soddisfino i requisiti di DWU.
5. Apportare modifiche finché non si raggiunge un livello di prestazioni ottimale per i propri requisiti aziendali.

Se si dispone di un'applicazione con un carico di lavoro molto variabile, è possibile aumentare o diminuire i livelli di prestazioni per far fronte ai picchi positivi e negativi. Ad esempio, se un carico di lavoro in genere fa registrare un picco alla fine del mese, è possibile pianificare l'aggiunta di altre DWU durante questi giorni di picco e quindi tornare al normale funzionamento al termine del periodo critico.
 
## Aumento e riduzione delle risorse di calcolo
L'elasticità di SQL Data Warehouse consente di aumentare, ridurre o sospendere la potenza di calcolo usando una scala scorrevole di unità data warehouse (DWU) in modo del tutto indipendente dall'archiviazione nel cloud. Questo conferisce la flessibilità necessaria per regolare la potenza di calcolo in modo ottimale per la propria azienda.

Per aumentare la potenza di calcolo, è possibile usare il [portale di Azure][], T-SQL, le API REST oppure PowerShell. L'aumento o la riduzione annulla tutte le attività in esecuzione o in coda, ma avviene in pochi secondi, pertanto è possibile riprendere con più o meno potenza di calcolo.

Il codice T-SQL seguente illustra come modificare l'allocazione di DWU per la propria istanza di SQL Data Warehouse:

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

È possibile ottenere lo stesso risultato con PowerShell usando il codice seguente:

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## Sospensione delle risorse di calcolo
Una caratteristica unica di SQL Data Warehouse è la possibilità di sospendere e riprendere il calcolo su richiesta. Se il team non deve usare l'istanza di Data Warehouse per un certo periodo di tempo, ad esempio la notte, nei week-end, per alcuni giorni festivi o per qualsiasi altro motivo, è possibile sospenderla per tale periodo di tempo e quindi riprendere dallo stesso punto al proprio ritorno.

L'azione di sospensione riporta le risorse di calcolo nel pool delle risorse disponibili all'interno del data center, mentre l'azione di ripresa acquisisce le risorse di calcolo necessarie per le DWU impostate e le assegna all'istanza di Data Warehouse.

Per sospendere e riprendere la potenza di calcolo, è possibile usare il [portale di Azure][], le API REST oppure PowerShell. Con la sospensione vengono annullate tutte le attività in esecuzione o in coda e, al ritorno, è possibile riprendere le risorse di calcolo nel giro di qualche secondo.

Il codice seguente illustra come eseguire una sospensione tramite PowerShell:

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

La ripresa del servizio è altrettanto semplice con PowerShell:

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

Per altri dettagli su come usare PowerShell, vedere l'articolo di [introduzione ai cmdlet di PowerShell][].

> [Azure.Note]Poiché l'archiviazione è separata dal calcolo, la sospensione non ha alcun effetto su di essa.

## Passaggi successivi
Per qualche cenno preliminare sulle prestazioni, vedere la [panoramica sulle prestazioni][].

<!--Image references-->

<!--Article references-->
[panoramica sulle prestazioni]: sql-data-warehouse-overview-performance.md
[introduzione ai cmdlet di PowerShell]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[portale di Azure]: http://portal.azure.com/

<!---HONumber=July15_HO4-->