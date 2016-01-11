   <properties
   pageTitle="Caricare i dati in SQL Data Warehouse | Microsoft Azure"
   description="Informazioni sugli scenari comuni per il caricamento dei dati in SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/17/2015"
   ms.author="lodipalm;barbkess"/>

# Caricare i dati in SQL Data Warehouse
SQL Data Warehouse presenta numerose opzioni per il caricamento dei dati tra cui:

- PolyBase
- Data factory di Azure
- Utilità della riga di comando BCP
- SQL Server Integration Services (SSIS)
- Strumenti di caricamento dati di terze parti

Mentre tutti i metodi precedenti possono essere utilizzati con SQL Data Warehouse, la capacità di PolyBase di parallelizzare in modo trasparente i carichi dall'archiviazione BLOB di Azure lo renderà lo strumento più veloce per il caricamento dei dati. Ulteriori informazioni su come [caricare con PolyBase][]. Inoltre, dato che molti dei nostri utenti guardano il caricamento iniziale in 100s di GB per il numero 10 di terabyte dalle origini locali, nelle sezioni di seguito vengono indicate alcune indicazioni sul caricamento dei dati iniziale.

## Caricamento iniziale in SQL Data Warehouse da SQL Server 
Durante il caricamento in SQL Data Warehouse da un'istanza di SQL Server locale, si consiglia quanto segue:

1. Dati **BCP** di SQL Server nei file flat 
2. Utilizzare **AZCopy** o **Importazione/Esportazione** (per i set di dati di dimensioni maggiori) per spostare i file in Azure
3. Configurare PolyBase per leggere i file dall'account di archiviazione
4. Creare nuove tabelle e caricare i dati con **PolyBase**

Nelle sezioni seguenti verrà analizzato in dettaglio ogni passaggio e verranno forniti esempi del processo.

> [AZURE.NOTE]Prima di spostare dati da un sistema, ad esempio SQL Server, è consigliabile rivedere gli articoli [Migrazione dello schema][] e [Migrazione del codice][] della nostra documentazione.

## Esportazione di file con BCP

Per preparare i file per lo spostamento in Azure, è necessario esportarli in file flat. Il modo migliore per eseguire questa operazione è utilizzare l’utilità della riga di comando BCP. Se non si dispone ancora dell'utilità, è possibile scaricarla con [Microsoft Command Line Utilities for SQL Server][]. Un comando BCP di esempio potrebbe essere il seguente:

```
bcp "<Directory><File>" -c -T -S <Server Name> -d <Database Name>
```

Questo comando acquisisce i risultati di una query e li esporta in un file nella directory di propria scelta. È possibile parallelizzare il processo tramite l'esecuzione di più comandi BCP per tabelle separate in una sola volta. In questo modo sarà possibile eseguire un processo BCP per ogni core del server; il consiglio è provare alcune operazioni di volume inferiore in configurazioni diverse per vedere cosa è più adatto per l'ambiente.

Inoltre, dato che il caricamento viene effettuato mediante PolyBase, tenere presente che PolyBase non supporta ancora UTF-16 e tutti i file devono essere in formato UTF-8. A tale scopo è possibile semplicemente utilizzare il flag '-c' nel comando BCP oppure convertire i file flat da UTF-16 in UTF-8 con il codice seguente:

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```
 
Una volta esportati correttamente i dati nei file, è possibile spostarli in Azure. Per questa operazione è possibile utilizzare AZCopy o il servizio di Importazione/Esportazione, come descritto nella sezione successiva.

## Caricamento in Azure mediante AZCopy o Importazione/Esportazione
Se si stanno spostando dati dell’ordine di 5-10 terabyte e oltre, è consigliabile utilizzare il servizio di trasferimento su disco [Importazione/Esportazione][] per eseguire lo spostamento. Tuttavia, nei nostri studi, siamo stati in grado di spostare dati nell'intervallo di una sola cifra di TB, comodamente tramite la rete Internet pubblica con AZCopy. Questo processo può inoltre essere accelerato o esteso con ExpressRoute.

La procedura seguente fornisce i dettagli su come spostare dati in locale in un account di archiviazione di Azure tramite AZCopy. Se non si dispone di un account di archiviazione di Azure nella stessa area è possibile crearne uno seguendo la [Documentazione di archiviazione di Azure][]. È inoltre possibile caricare dati da un account di archiviazione in un'area diversa, ma le prestazioni in questo caso non saranno ottimali.

> [AZURE.NOTE]Nella presente documentazione si presuppone che sia stata installata l'utilità della riga di comando AZCopy e che si sia in grado di eseguirla con Powershell. Se questo non è il caso, seguire le [Istruzioni di installazione di AZCopy][].

A questo punto, dato un set di file creati utilizzando BCP, AzCopy può semplicemente essere eseguito da Azure PowerShell o tramite uno script PowerShell. In generale, il formato del prompt dei comandi necessario per l'esecuzione di AZCopy sarà:

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

Oltre alla procedura base, per il caricamento con AZCopy si raccomandano le seguenti procedure consigliate:


+ **Connessioni simultanee**: oltre ad aumentare il numero di operazioni di AZCopy eseguite contemporaneamente, il funzionamento stesso di AZCopy può essere ulteriormente parallelizzato impostando il parametro /NC che consente di aprire più connessioni simultanee alla destinazione. Sebbene il parametro possa essere impostato su un valore massimo di 512, è stato riscontrato che il valore ottimale di trasferimento dei dati è 256, quindi si consiglia di verificare vari valori in modo da individuare quello ottimale per la configurazione in uso.

+ **Express Route**: come indicato in precedenza, questo processo può essere accelerato abilitando Express Route. Una panoramica di Express Route e dei passaggi di configurazione è disponibile nella [documentazione di ExpressRoute][].

+ **Struttura di cartelle**: per agevolare il trasferimento con PolyBase, verificare che ogni tabella sia mappata alla propria cartella. In tal modo verranno ridotte al minimo e semplificate le operazioni durante il successivo caricamento con PolyBase. Detto questo, la suddivisione di una tabella in più file o anche in sottodirectory all’interno della stessa cartella non produce alcun impatto negativo.
	 

## Configurazione di PolyBase 

Ora che i dati risiedono nei blob di archiviazione di Azure, verranno importati nell'istanza di SQL Data Warehouse utilizzando PolyBase. I passaggi riportati di seguito riguardano esclusivamente la configurazione e molti di essi dovranno essere completati una sola volta per account di archiviazione, utente o istanza di SQL Data Warehouse. Questa procedura è descritta con maggiore dettaglio nella documentazione [Caricare con PolyBase][].

1. **Creare la chiave master del database.** Questa operazione dovrà essere completata una volta sola per ogni database. 

2. **Creare una credenziale con ambito di database.** Questa operazione è necessaria soltanto se si desidera creare una nuova credenziale/un nuovo utente, altrimenti è possibile utilizzare la credenziale creata in precedenza.

3. **Creare un formato di file esterno.** Anche i formati di file esterni sono riutilizzabili, è necessario crearne uno solo se si sta caricando un nuovo tipo di file.

4. **Creare un’origine dati esterna.** Puntando a un account di archiviazione, è possibile utilizzare un'origine dati esterna durante il caricamento dallo stesso contenitore. Per il parametro 'LOCATION', utilizzare un percorso del formato: 'wasbs://mycontainer@ test.blob.core.windows.net/path'.

```
-- Creating master key
CREATE MASTER KEY;

-- Creating a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL <Credential Name> 
WITH 
    IDENTITY = '<User Name>'
,   Secret = '<Azure Storage Key>'
;

-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(
    FORMAT_TYPE = DELIMITEDTEXT 
,   FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|' 
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
);

--Creating an external data source
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH 
(
    TYPE = HADOOP 
,   LOCATION ='wasbs://<Container>@<Blob Path>'
,   CREDENTIAL = <Credential Name>
)
;
```

Ora che l'account di archiviazione è configurato correttamente è possibile procedere al caricamento dei dati in SQL Data Warehouse.

## Caricamento dei dati con PolyBase 
Dopo aver configurato PolyBase, è possibile caricare i dati direttamente in SQL Data Warehouse creando semplicemente una tabella esterna che punti ai dati nella risorsa di archiviazione ed eseguendo il mapping di tali dati in una nuova tabella all'interno di SQL Data Warehouse. A tale scopo è possibile utilizzare i due semplici comandi riportati di seguito.

1. Utilizzare il comando "CREATE EXTERNAL TABLE" per definire la struttura dei dati. Per acquisire lo stato dei dati in modo rapido ed efficiente, è consigliabile preparare lo script della tabella di SQL Server in SSMS, provvedendo poi alla modifica manuale per inserire le differenze della tabella esterna. Dopo aver creato una tabella esterna in Azure, questa continuerà a puntare alla stessa posizione anche se i dati vengono aggiornati o si aggiungono altri dati.  

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE <External Table Name> 
(
    <Column name>, <Column type>, <NULL/NOT NULL>
)
WITH 
(   LOCATION='<Folder Path>'
,   DATA_SOURCE = <Data Source>
,   FILE_FORMAT = <File Format>      
);
```

2. Caricare i dati con un’istruzione 'CREATE TABLE...AS SELECT’. 

```
CREATE TABLE <Table Name> 
WITH 
(
	CLUSTERED COLUMNSTORE INDEX,
	DISTRIBUTION = <HASH(<Column Name>)>/<ROUND_ROBIN>
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

Si noti che è inoltre possibile caricare una sottosezione delle righe da una tabella utilizzando un'istruzione SELECT più dettagliata. Tuttavia, dato che PolyBase a questo punto non impone ulteriore elaborazione sugli account di archiviazione, il caricamento di una sottosezione con un'istruzione SELECT non sarà più rapido del caricamento dell’intero set di dati.

Oltre all’istruzione `CREATE TABLE...AS SELECT` è inoltre possibile caricare dati da tabelle esterne in tabelle preesistenti utilizzando l’istruzione 'INSERT...INTO'.

##  Creare le statistiche sui dati appena caricati 

SQL Data Warehouse di Azure non supporta ancora le statistiche di creazione automatica o aggiornamento automatico. Per ottenere le migliori prestazioni dalle query, è importante creare statistiche per tutte le colonne di tutte le tabelle dopo il primo caricamento o dopo eventuali modifiche sostanziali dei dati. Per una spiegazione dettagliata delle statistiche, vedere l'argomento [Statistiche][] nel gruppo di argomenti sullo sviluppo. Di seguito è possibile vedere un rapido esempio di come creare statistiche nella tabella caricata in questo esempio.


```
create statistics [<name>] on [<Table Name>] ([<Column Name>]);
create statistics [<another name>] on [<Table Name>] ([<Another Column Name>]);
```

## Passaggi successivi
Per altri suggerimenti relativi allo sviluppo, vedere la [panoramica sullo sviluppo][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[caricare con PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[panoramica sullo sviluppo]: sql-data-warehouse-overview-develop.md
[Migrazione dello schema]: sql-data-warehouse-migrate-schema.md
[Migrazione del codice]: sql-data-warehouse-migrate-code.md
[Statistiche]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[Istruzioni di installazione di AZCopy]: https://azure.microsoft.com/it-IT/documentation/articles/storage-use-azcopy/
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/it-IT/download/details.aspx?id=36433
[Importazione/Esportazione]: https://azure.microsoft.com/it-IT/documentation/articles/storage-import-export-service/
[Documentazione di archiviazione di Azure]: https://azure.microsoft.com/it-IT/documentation/articles/storage-create-storage-account/
[documentazione di ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

<!---HONumber=AcomDC_1223_2015-->