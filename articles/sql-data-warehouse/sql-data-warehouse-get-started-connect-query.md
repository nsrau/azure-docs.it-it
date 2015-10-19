<properties
   pageTitle="Introduzione: Connettersi ad Azure SQL Data Warehouse | Microsoft Azure"
   description="Introduzione alla connessione a SQL Data Warehouse e all'esecuzione di alcune query."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/01/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Introduzione: Connettersi ad Azure SQL Data Warehouse
Questo breve articolo introduttivo illustra la connessione e l'esecuzione di query in un'istanza con provisioning di SQL Data Warehouse con due strumenti diversi:

- **Visual Studio**: l'editor di codice e il debugger integrati di Visual Studio, ovvero SQL Server Data Tools (SSDT), sono completamente compatibili con SQL Data Warehouse e consentono di connettersi, eseguire query e gestire con facilità SQL Data Warehouse.  

> [AZURE.NOTE]L’SQL Data Warehouse richiede almeno la versione di anteprima di SSDT 12.0.50623 o versione successiva.

- **sqlcmd**: è uno strumento da riga di comando che offre semplici capacità di connessione ed esecuzione di query.  

Al termine di questo articolo saranno state eseguite le operazioni seguenti:

1. Installazione e aggiornamento di Visual Studio 2013
2. Creazione di una connessione a SQL Data Warehouse in SSDT
3. Esecuzione di query sul database di SQL Data Warehouse

>[AZURE.NOTE]Si presuppone che siano state completate le procedure disponibili nella guida al provisioning o che SQL Data Warehouse sia disponibile. Se non è stato eseguito il provisioning di SQL Data Warehouse, fare riferimento alla [guida introduttiva sul provisioning](sql-data-warehouse-get-started-provision.md).

## Configurare Visual Studio per lo sviluppo##
Per lo sviluppo, il team di SQL Data Warehouse consiglia di usare Visual Studio 2013 o versioni successive insieme a SQL Server Data Tools. Di seguito viene illustrato come scaricare e aggiornare Visual Studio 2013, se non è già disponibile una versione valida di Visual Studio installata.

Se sono necessarie altre informazioni, i dubbi generali relativi a SSDT possono essere risolti mediante la [documentazione completa su SSDT](https://msdn.microsoft.com/library/azure/hh272686.aspx).

### Ottenere Visual Studio 2013 ###
Passare al sito Web di Visual Studio 2013 per scaricare e installare una copia di Visual Studio. Qualsiasi edizione gratuita è più che adeguata per SQL Data Warehouse. È quindi possibile scegliere l'edizione più adatta alle proprie esigenze.

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Ottenere Visual Studio 2013</a>

### Aggiornare Visual Studio 2013 ###
Se Visual Studio 2013 è già installato, non è necessario installarlo di nuovo. Per assicurarsi che sia aggiornato, è sufficiente eseguire i passaggi seguenti. È possibile procedere al passaggio successivo.

1. Aprire Visual Studio 2013.
2. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**.
3. Nel controllo albero passare ad **Aggiornamenti** e **Aggiornamenti del prodotto**.
4. Se non sono disponibili aggiornamenti, è possibile chiudere la finestra **Estensioni e aggiornamenti** e passare all'attività successiva di questa guida introduttiva.

Se invece è disponibile un aggiornamento per Visual Studio, fare clic sul pulsante **Aggiorna**. Verrà avviato il download dell'aggiornamento più recente per Visual Studio 2013.

Prima di continuare, chiudere la finestra **Estensioni e aggiornamenti** e chiudere Visual Studio 2013.

5. Fare clic sul pulsante **Esegui** per installare l'aggiornamento più recente per Visual Studio 2013.

6. Accettare le condizioni di licenza e fare clic sul pulsante **Installa**, accettando eventuali richieste relative al Controllo dell'account utente.

7. Fare clic sul pulsante **Avvia** per completare l'installazione.

L'aggiornamento di Visual Studio 2013 è stato completato.

### Aggiornare SSDT
> [AZURE.IMPORTANT]L’SQL Data Warehouse richiede almeno la versione di anteprima di SSDT 12.0.50623 o versione successiva.

I tecnici che si occupano di SSDT aggiornano regolarmente il plug-in con nuove funzionalità e ogni tanto sarà quindi necessario applicare gli aggiornamenti. Si tratta comunque di un processo molto semplice. Per verificare se è necessario aggiornare SSDT, seguire questa procedura:

1. Aprire Visual Studio 2013.  
2. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**.
3. Nel controllo albero passare ad **Aggiornamenti** e **Aggiornamenti del prodotto**.
4. Se non sono disponibili aggiornamenti, è possibile chiudere la finestra **Estensioni e aggiornamenti** e passare all'attività successiva di questa guida introduttiva.

Se invece è disponibile un aggiornamento definito "Aggiornamento Microsoft SQL Server per strumenti di database", fare clic sul pulsante **Aggiorna**. Verrà avviato il download della versione più recente di SSDT.

5. Fare clic sul pulsante **Esegui** per installare la versione più recente di SSDT.

6. Accettare le condizioni di licenza e quindi fare clic sul pulsante **Installa**.

7. Fare clic sul pulsante **Chiudi** per completare l'aggiornamento di SSDT.

8. Chiudere la finestra **Estensioni e aggiornamenti**.

Sul desktop è ora disponibile una versione aggiornata di Visual Studio 2013 con un'estensione SSDT aggiornata.

> [AZURE.NOTE]È attualmente consigliabile l'[anteprima di SSDT per Visual Studio 2013 versione 12.0.50623 o successive](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409).

## Connettersi a Visual Studio 2013
Se si esegue la versione desiderata di Visual Studio, sarà possibile connettersi all'istanza di SQL Data Warehouse in due modi diversi.

### Connettersi da Visual Studio
Per stabilire la connessione, è sufficiente aprire Esplora oggetti di SQL Server e specificare le informazioni di connessione.

1. Aprire Visual Studio.
2. Scegliere **Esplora oggetti di SQL Server** dal menu **Visualizza**.

> [AZURE.NOTE]Assicurarsi di selezionare Esplora oggetti di SQL Server, *non* Esplora server. I nomi sono simili, ma si tratta di controlli molto diversi. Si trovano uno vicino all'altro, quindi è necessario accertarsi di avere selezionato il controllo corretto.

È ora possibile visualizzare Esplora oggetti di SQL Server:


3. Fare clic sul pulsante **Aggiungi server** in Esplora oggetti di SQL Server.

4. Nella finestra di dialogo **Connetti al server** inserire i valori scelti durante la creazione del server logico. Inoltre, fare clic sul pulsante Opzioni e specificare il database a cui si desidera connettersi (l'istanza di SQL Data Warehouse) prima della connessione.

Se lo si desidera, è anche possibile selezionare la casella **Memorizza password**, che permette di risparmiare tempo. È necessario però ricordare che in questo modo si consente a chiunque possa accedere fisicamente al profilo di eseguire query usando questo account.

> [AZURE.NOTE]Si tenga presente che il nome del server deve essere completo. Il valore del nome server deve quindi essere conforme a questa convenzione: *NomeServer*.database.windows.net, dove *NomeServer* è il nome assegnato al server logico.

Dopo avere specificato tutte le credenziali necessarie, fare clic su **Connetti**.

La connessione è stata completata e il server logico SQL Data Warehouse è stato registrato in Esplora oggetti di SQL Server.

### Connettersi dal portale di Azure
Passare a Visual Studio direttamente dal portale di Azure.

1. Accedere al [portale di Azure](http://manage.windowsazure.com/).
2. Selezionare l'istanza desiderata di SQL Data Warehouse nel pannello **Sfoglia**.
3. Nella parte superiore del pannello SQL Data Warehouse selezionare **Apri in Visual**.
4. Se il firewall non è stato configurato con l'indirizzo IP del computer client, selezionare **Configura il firewall**.

	a. Immettere i valori per **Nome regola**, **Indirizzo IP iniziale** e **Indirizzo IP finale**.

	b. Fare clic su **Salva** nella parte superiore del pannello.
5. Fare clic su **Apri in Visual Studio**.
6. Visual Studio verrà aperto e verranno richieste le credenziali.
7. Dopo l'immissione delle credenziali e la connessione, il server e le istanze di Data Warehouse verranno visualizzati nel pannello di Esplora oggetti di SQL Server.  

## Connettersi a SQL Data Warehouse con sqlcmd

È anche possibile connettersi a SQL Data Warehouse con l'utilità del prompt dei comandi [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) inclusa in SQL Server o con [Microsoft Command Line Utilities 11 for SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501). L'utilità sqlcmd permette di immettere istruzioni Transact-SQL, procedure di sistema e file script al prompt dei comandi.

Per connettersi a un'istanza specifica di SQL Data Warehouse quando si usa sqlcmd, sarà necessario aprire il prompt dei comandi e digitare **sqlcmd** seguito dalla stringa di connessione per il database di SQL Data Warehouse. La stringa di connessione deve includere i parametri seguenti:

+ **Utente (-U):** utente del server nel formato `<`Utente`>`@`<`Nome server`>`.database.windows.net
+ **Password (-P):** password associata all'utente
+ **Server (-S):** server nel formato `<`Nome server`>`.database.windows.net
+ **Database (-D):** nome del database
+ **Abilita identificatori delimitati (-I):** gli identificatori delimitati devono essere abilitati per consentire la connessione a un'istanza di SQL Data Warehouse.

Per connettersi a un'istanza di SQL Data Warehouse, è quindi necessario immettere quanto segue:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

Dopo la connessione sarà possibile eseguire qualsiasi istruzione Transact-SQL supportata nell'istanza. Ad esempio, l'istruzione seguente usa l'istruzione [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) per creare una nuova tabella.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```

Per altre informazioni su sqlcmd, vedere la [relativa documentazione](https://msdn.microsoft.com/library/azure/ms162773.aspx).

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## Eseguire query di esempio ##

Dopo avere registrato il server, è possibile scrivere una query.

1. Fare clic sul database utente in SSDT.

2. Fare clic sul pulsante **Nuova query**. Verrà visualizzata una nuova finestra.

3. Scrivere una query. Immettere il codice seguente nella finestra della query:

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. Eseguire la query.

	Per eseguire la query, fare clic sulla freccia verde oppure usare la combinazione di tasti seguente: `CTRL`+`SHIFT`+`F5`.

## Passaggi successivi ##
Ora che è possibile connettersi ed eseguire query, tentare il [caricamento dei dati di esempio][].

[caricamento dei dati di esempio]: ./sql-data-warehouse-get-started-load-samples.md
[developing code]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=Oct15_HO2-->