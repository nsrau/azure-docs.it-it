<properties linkid="manage-services-how-to-scale-a-sqldb" urlDisplayName="How to scale" pageTitle="How to scale a SQL Database - Azure" metaKeywords="" description="Learn about options for scaling your SQL Database in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Scale a SQL Database Solution" authors="" solutions="" manager="" editor="" />

Come scalare una soluzione di database SQL
==========================================

In Azure la scalabilità dei database è sinonimo di scalabilità orizzontale, in cui un carico di lavoro viene ridistribuito tra più commodity server in un data center. La scalabilità orizzontale rientra in una strategia tesa ad affrontare i problemi di capacità dei dati o prestazioni, che si rende necessaria per database di dimensioni molto grandi e per cui è prevista una crescita elevata, indipendentemente dal numero di utenti che vi accederanno.

In Azure la soluzione ottimale per implementare la scalabilità orizzontale è rappresentata dalla federazione. La federazione del database SQL è basata sul partizionamento orizzontale, in cui una o più tabelle vengono divise per riga e distribuite tra più membri della federazione.

La federazione non è però l'unica risposta a tutti i problemi di scalabilità. A volte le caratteristiche dei dati o i requisiti delle applicazioni consentono anche approcci più semplici. Nell'elenco seguente sono presentate le possibili soluzioni in ordine di complessità.

Aumentare le dimensioni del database
------------------------------------

I database vengono creati con dimensioni fisse soggette a un limite massimo imposto da ogni edizione. Per l'edizione Web, è possibile aumentarle fino a un massimo di 5 gigabyte. Per l'edizione Business, le dimensioni massime del database sono di 150 gigabyte. Il modo più ovvio per incrementare la capacità dei dati consiste nel cambiare l'edizione e le dimensioni massime:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

Utilizzare più database e allocare gli utenti
---------------------------------------------

In scenari limitati è possibile creare copie di un database e quindi allocare account di accesso e utenti per ognuna di esse. Prima che venisse resa disponibile la federazione, era questo l'approccio comune per ridistribuire un carico di lavoro. Questo approccio è fattibile per i database utilizzati per brevi periodi di tempo e in seguito uniti a un database primario mantenuto in locale, oltre che per soluzioni che forniscono dati in sola lettura.

Utilizzare le federazioni
-------------------------

Nel database SQL le federazioni vengono utilizzate per raggiungere livelli superiori di scalabilità e prestazioni. Una o più tabelle all'interno di un database vengono divise per riga e distribuite tra più database, ovvero i membri della federazione. Si tratta in sostanza di un partizionamento orizzontale. I principali scenari in cui questo approccio risulta utile sono quelli in cui è necessario ottenere scalabilità e prestazioni oppure gestire la capacità.

Le federazioni sono supportate nell'edizione Business. Per ulteriori informazioni, vedere [Federazioni in database SQL di Azure](http://msdn.microsoft.com/en-us/library/windowsazure/hh597452.aspx) e [Esercitazione sulle federazioni del database SQL - DBA](http://msdn.microsoft.com/en-us/library/windowsazure/hh778416.aspx).

Valutare altri tipi di archiviazione
------------------------------------

Tenere presente che Azure supporta più tipi di archiviazione dati, tra cui tabelle e BLOB. Se non sono necessarie caratteristiche relazionali, l'archiviazione tabelle o BLOB può risultare più economicamente vantaggiosa.

