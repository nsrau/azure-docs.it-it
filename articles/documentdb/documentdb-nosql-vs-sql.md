<properties
	pageTitle="Quando usare NoSQL o SQL | Microsoft Azure"
	description="Confrontare i vantaggi dell'uso di soluzioni non relazionali NoSQL e di soluzioni SQL. Informazioni per stabilire se sia più adatto al proprio scenario uno dei servizi NoSQL di Microsoft Azure o SQL Server."
	keywords="NoSQL e SQL, quando usare NoSQL, SQL e NoSQL"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/24/2016"
	ms.author="mimig"/>

# NoSQL e SQL

SQL Server e i database relazionali (RDBMS) hanno costituito l'ideale di database per più di 20 anni, ma la sempre maggiore necessità di elaborare velocemente volumi più elevati e diversi tipi di dati ha cambiato la natura delle esigenze di archiviazione per gli sviluppatori di applicazioni. Nell'ambito di questo scenario, i database NoSQL che consentono l'archiviazione di dati non strutturati ed eterogenei su larga scala sono diventati sempre più popolari.

NoSQL è una categoria di database del tutto diversa dai database SQL. Il termine NoSQL viene spesso usato in riferimento ai sistemi di gestione dati "non SQL" o per indicare un approccio alla gestione dati che includa "non solo SQL". La categoria NoSQL include diverse tecnologie, tra cui database di documenti, archivi di valori chiave, archivi di famiglie di colonne e database di grafici, molto comuni nelle app di gioco, per i social network e IoT.

![Diagramma della panoramica di NoSQL e SQL che illustra gli scenari e i modelli di dati comuni](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

L'obiettivo di questo articolo è illustrare le differenze tra NoSQL e SQL e presentare le offerte relative a NoSQL e SQL di Microsoft.

## Quando usare NoSQL?

Si immagini di compilare un nuovo sito di social engagement. Gli utenti possono creare post a cui aggiungere immagini, video e brani musicali. Gli altri utenti possono commentare i post e assegnare punti (mi piace) per classificare i post. La pagina di destinazione includerà un feed di post che gli utenti possono condividere e con cui possono interagire.

Come si archiviano quindi questi dati? Se si ha familiarità con SQL, è possibile iniziare a progettare qualcosa di simile a questo:

![Diagramma di NoSQL e SQL che illustra il modello di dati relazionale per un sito di social engagement](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

Ora però si consideri la struttura di un singolo post e come visualizzarla. Per visualizzare il post e le immagini, il video, i commenti, i punti e le informazioni utente associati in un sito Web o in un'applicazione, sarà necessario eseguire una query con otto join di tabella solo per recuperare il contenuto. Si pensi ora a un flusso di post che vengono caricati e visualizzati in modo dinamico. Come si può immaginare, saranno necessarie migliaia di query e numerosi join per completare l'attività.

Ora è possibile usare una soluzione relazionale come SQL Server per archiviare i dati, ma esiste un'altra opzione, un'opzione NoSQL che semplifica l'approccio. Trasformando il post in un documento JSON come il seguente e archiviandolo in DocumentDB, un servizio database di documenti NoSQL di Azure, è possibile aumentare le prestazioni e recuperare l'intero post con una query e nessun join. È un risultato più semplice, più facile e più efficace.

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Questi dati possono anche essere partizionati in base all'ID post consentendo l'aumento naturale del numero di istanze dei dati e sfruttando le caratteristiche di scalabilità di NoSQL. I sistemi NoSQL consentono agli sviluppatori anche di ridurre la coerenza e di offrire app a disponibilità elevata. Infine questa soluzione non richiede agli sviluppatori di definire, gestire e mantenere lo schema nel livello dati consentendo una rapida iterazione.

È quindi possibile compilare questa soluzione usando altri servizi di Azure:

- [Ricerca di Azure](https://azure.microsoft.com/services/search/) può essere usato tramite l'app Web per consentire agli utenti di cercare i post.
- [Servizi app di Azure](https://azure.microsoft.com/services/app-service/) può essere usato per ospitare applicazioni e processi in background.
- [Archivio BLOB di Azure](https://azure.microsoft.com/services/storage/) può essere usato per archiviare i profili utente completi, incluse le immagini.
- [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) può essere usato per archiviare grandi quantità di dati, ad esempio le informazioni di accesso, e i dati per l'analisi di utilizzo.
- [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) può essere usato per creare conoscenze e informazioni che possono fornire feedback al processo e consentono di distribuire il contenuto a seconda degli utenti.

Questo sito di social engagement è solo uno scenario in cui un database NoSQL è il modello di dati appropriato per il processo. Per altre informazioni su questo scenario e su come modellare i dati per DocumentDB in applicazioni per social media, vedere [Integrazione con i social con DocumentDB](documentdb-social-media-apps.md).

## Confronto tra NoSQL e SQL

La tabella seguente confronta le principali differenze tra NoSQL e SQL.

![Diagramma di NoSQL e SQL che illustra quando usare NoSQL e quando usare SQL. Confronto tra SQL e NoSQL](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

Se un database NoSQL è più adatto ai propri requisiti, continuare con la sezione successiva per saperne di più sui servizi NoSQL disponibili in Azure. In caso contrario, se un database SQL è più adatto alle proprie esigenze, passare a [Quali sono le offerte per Microsoft SQL?](#what-are-the-microsoft-sql-offerings)

## Quali sono le offerte per NoSQL di Microsoft Azure?

Azure ha quattro servizi NoSQL completamente gestiti:

- [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
- [Archivio tabelle di Azure](https://azure.microsoft.com/services/storage/)
- [HBase di Azure come parte di HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Cache Redis di Azure](https://azure.microsoft.com/services/cache/)

Il grafico di confronto seguente evidenzia i principali fattori di differenziazione per ogni servizio. Quale descrive in modo più preciso le esigenze dell'applicazione?

![Diagramma di NoSQL e SQL che illustra quando usare le offerte per NoSQL di Microsoft Azure, inclusi DocumentDB, l'archivio tabelle, HBase come parte di HDInsight e Redis Cache](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

Se uno o più di questi servizi possono soddisfare le esigenze dell'applicazione, è possibile ottenere altre informazioni dalle risorse seguenti:

- [Percorso di apprendimento per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) e [Casi di utilizzo di DocumentDB](documentdb-use-cases.md)
- [Introduzione all'archivio tabelle di Azure](../storage/storage-dotnet-how-to-use-tables.md)
- [Che cos'è HBase in HDInsight](../hdinsight/hdinsight-hbase-overview.md)
- [Percorso di apprendimento di Cache Redis](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

Per informazioni sulla versione di prova gratuita, andare a [Passaggi successivi](#next-steps).

## Quali sono le offerte per Microsoft SQL?

Microsoft ha cinque offerte per SQL:

- [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/)
- [SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
- [Azure SQL Data Warehouse (anteprima)](https://azure.microsoft.com/services/sql-data-warehouse/)
- [Piattaforma di strumenti analitici (appliance locale)](https://www.microsoft.com/it-IT/server-cloud/products/analytics-platform-system/)

Se si è interessati a SQL Server in una macchina virtuale o al database SQL, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) per altre informazioni sulle differenze tra le due opzioni.

Se si ritiene che SQL sia l'opzione migliore, vedere [SQL Server](https://www.microsoft.com/server-cloud/products/) per altre informazioni sulle caratteristiche dei prodotti e servizi Microsoft SQL.

Per informazioni sulla versione di prova gratuita e i collegamenti per la valutazione, andare a [Passaggi successivi](#next-steps).

## Passaggi successivi

Per conoscere meglio i prodotti SQL e NoSQL, è possibile provarli gratuitamente.

- Per tutti i servizi di Azure, è possibile iscriversi a una [versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/) e ricevere 200 dollari da spendere per qualsiasi servizio di Azure.
    - [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
    - [HBase di Azure come parte di HDInsight](https://azure.microsoft.com/services/hdinsight/)
    - [Cache Redis di Azure](https://azure.microsoft.com/services/cache/)
    - [Azure SQL Data Warehouse (anteprima)](https://azure.microsoft.com/services/sql-data-warehouse/)
    - [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/)
    - [Archivio tabelle di Azure](https://azure.microsoft.com/services/storage/)

- È possibile attivare una [versione di valutazione di SQL Server 2016 in una macchina virtuale](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/) o scaricare una [versione di valutazione di SQL Server](https://www.microsoft.com/it-IT/evalcenter/evaluate-sql-server-2016).
    - [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
    - [SQL Server in Macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)

<!---HONumber=AcomDC_0727_2016-->