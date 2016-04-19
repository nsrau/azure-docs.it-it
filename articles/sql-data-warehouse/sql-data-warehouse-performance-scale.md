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
   ms.date="03/29/2016"
   ms.author="nicw;jrj;mausher;barbkess;sonyama"/>

# Scalabilità e prestazioni elastiche con SQL Data Warehouse
Per aumentare o ridurre in modo elastico la potenza di calcolo di cui si dispone, è sufficiente modificare il numero delle unità data warehouse (DWU) allocate alla propria istanza di SQL Data Warehouse. Le unità data warehouse sono un nuovo concetto introdotto con SQL Data Warehouse per consentirne una gestione semplice ed efficace. Questo argomento è un'introduzione alle unità data warehouse in cui viene spiegato come usarle per scalare in modo elastico la potenza di calcolo. L'articolo fornisce anche alcune indicazioni iniziali su come impostare un valore DWU ragionevole per l'ambiente in uso.

## Che cos'è un'unità data warehouse?
Microsoft di fondo esegue diversi test benchmark delle prestazioni per stabilire quanto hardware e quale configurazione consentiranno di fornire un'offerta competitiva ai clienti. Il calcolo può essere aumentato o ridotto in blocchi di 100 DWU, ma non tutti i multipli di 100 DWU sono disponibili.

## Quante DWU è consigliabile usare?
Invece di fornire per le DWU punti di partenza vincolanti che possano essere appropriati per una categoria di clienti, la questione viene affrontata con un approccio pratico. Le prestazioni in SQL Data Warehouse vengono scalate in modo lineare e il passaggio da una scala di calcolo a un'altra (ad esempio, da 100 DWU a 2000 DWU) avviene nel giro di pochi secondi. Questo offre la flessibilità necessaria per provare le operazioni e determinare quella adatta allo scenario.

1. Per un data warehouse in sviluppo, iniziare con un numero limitato di DWU.
2. Monitorare le prestazioni dell'applicazione, osservare che il numero di DWUs selezionato in relazione alle prestazioni che si osservano.
3. Determinare di quanto si vogliono aumentare o ridurre le prestazioni per raggiungere il livello ottimale per i propri requisiti presupponendo una scalabilità lineare.
4. Aumentare o diminuire il numero di DWU selezionata. Il servizio risponderà rapidamente e adatterà le risorse di calcolo in modo che soddisfino i requisiti di DWU.
5. Continuare ad apportare modifiche finché non si raggiunge un livello di prestazioni ottimale per i propri requisiti aziendali.

Se si dispone di un'applicazione con un carico di lavoro molto variabile, aumentare o diminuire i livelli di prestazioni per far fronte ai picchi positivi e negativi. Ad esempio, se un carico di lavoro in genere fa registrare un picco alla fine del mese, pianificare l'aggiunta di altre DWU durante questi giorni di picco e quindi ridurre le dimensioni al termine del periodo critico.

## Aumento e riduzione delle risorse di calcolo
L'elasticità di SQL Data Warehouse consente di aumentare, ridurre o sospendere la potenza di calcolo usando una scala scorrevole di unità data warehouse (DWU) in modo del tutto indipendente dall'archiviazione nel cloud. Questo conferisce la flessibilità necessaria per regolare la potenza di calcolo in modo ottimale per la propria azienda.

Per aumentare la potenza di calcolo è possibile aggiungere ulteriori DWU al servizio utilizzando il dispositivo di scorrimento scala nel portale di Azure classico. È inoltre possibile aggiungere DWU tramite T-SQL, le API REST o i cmdlet di Azure Powershell. L'aumento o la riduzione annulla tutte le attività in esecuzione o in coda, ma avviene in pochi secondi, pertanto è possibile riprendere con più o meno potenza di calcolo.

Nel [portale di Azure classico][], è possibile fare clic sull'icona "Scalabilità" nella parte superiore della pagina SQL Data Warehouse e quindi utilizzare il dispositivo di scorrimento per aumentare o ridurre la quantità di DWU applicati al Data Warehouse prima di fare clic su "Salva". Se si vuole modificare la scalabilità a livello di programmazione, il codice T-SQL seguente illustra come modificare l'allocazione di DWU per il proprio SQL Data Warehouse:

```sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```
Si noti che questo T-SQL deve essere eseguito sul server logico e non sull’istanza stessa di SQL Data Warehouse.

È inoltre possibile ottenere lo stesso risultato con Azure Powershell mediante l'importazione del modulo AzureRM.Sql, usando il codice seguente:

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -RequestedServiceObjectiveName "DW1000"
```

## Sospensione delle risorse di calcolo
Una caratteristica unica di SQL Data Warehouse è la possibilità di sospendere e riprendere il calcolo su richiesta. Se il team non deve usare l'istanza di Data Warehouse per un certo periodo di tempo, ad esempio la notte, nei week-end, per alcuni giorni festivi o per qualsiasi altro motivo, è possibile sospenderla per tale periodo di tempo e quindi riprendere dallo stesso punto al proprio ritorno.

L'azione di sospensione riporta le risorse di calcolo nel pool delle risorse disponibili all'interno del data center, mentre l'azione di ripresa acquisisce le risorse di calcolo necessarie per le DWU impostate e le assegna all'istanza di Data Warehouse.

> [AZURE.NOTE] Poiché l'archiviazione è separata dal calcolo, la sospensione non ha alcun effetto su di essa.

Per sospendere e riprendere la potenza di calcolo, è possibile usare il [portale di Azure classico][], le API REST oppure Azure Powershell. Con la sospensione vengono annullate tutte le attività in esecuzione o in coda e, al ritorno, è possibile riprendere le risorse di calcolo nel giro di qualche secondo.

Prima di sospendere e riprendere il servizio usando Azure Powershell, è necessario importare il modulo AzureRM.Sql come indicato di seguito:

```Powershell
Import-Module AzureRM.Sql
```

Il codice seguente illustra come eseguire una sospensione tramite Azure PowerShell:

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

La ripresa del servizio è altrettanto semplice con Azure PowerShell:

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

Per altre informazioni sull'uso di Azure PowerShell consultare [Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse][].

## Passaggi successivi
Per qualche cenno preliminare sulle prestazioni, vedere la [panoramica sulle prestazioni][].

<!--Image references-->

<!--Article references-->
[panoramica sulle prestazioni]: sql-data-warehouse-overview-performance.md
[Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse]: sql-data-warehouse-reference-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[portale di Azure classico]: http://portal.azure.com/

<!---HONumber=AcomDC_0406_2016-->