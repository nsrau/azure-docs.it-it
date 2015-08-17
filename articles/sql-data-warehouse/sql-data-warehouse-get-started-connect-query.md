<properties
   pageTitle="Introduzione: Connettersi a SQL Data Warehouse |Microsoft Azure"
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
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

   
# Introduzione: Connettersi a SQL Data Warehouse
Questo breve articolo introduttivo illustra la connessione e l'esecuzione di query in un'istanza con provisioning di SQL Data Warehouse con due strumenti diversi:

**Visual Studio**: l'editor di codice e il debugger integrati di Visual Studio, ovvero SQL Server Data Tools (SSDT), sono completamente compatibili con SQL DW e permettono di connettersi, eseguire query e gestire con facilità SQL Data Warehouse.

> [AZURE.NOTE]L’SQL Data Warehouse richiede almeno la versione di anteprima di SSDT 12.0.50623 o versione successiva

**sqlcmd**: sqlcmd è uno strumento da riga di comando che offre semplici capacità di connessione ed esecuzione di query.

Al termine di questo articolo saranno state eseguite le operazioni seguenti:

1. Installazione e aggiornamento di Visual Studio 2013
2. Creazione di una connessione a SQL Data Warehouse in SSDT
3. Esecuzione di query nel database di SQL Data Warehouse

>[AZURE.NOTE]Si presuppone che siano state completate le procedure disponibili nella guida al provisioning o che SQL Data Warehouse sia disponibile. Se non è stato eseguito il provisioning di SQL Data Warehouse, fare riferimento alla [guida introduttiva sul provisioning].

## Configurazione di Visual Studio per lo sviluppo##
Per lo sviluppo, il team di SQL Data Warehouse consiglia di usare Visual Studio 2013 o versioni successive insieme a SQL Server Data Tools. Di seguito viene illustrato come scaricare e aggiornare Visual Studio 2013, se non è già disponibile una versione valida di Visual Studio installata.

Se sono necessarie altre informazioni, i dubbi generali relativi a SSDT possono essere risolti mediante la [documentazione completa su SSDT](https://msdn.microsoft.com/library/azure/hh272686.aspx).

### Ottenere Visual Studio 2013 ###
Passare al sito Web di Visual Studio 2013 per scaricare e installare una copia di Visual Studio. Qualsiasi edizione gratuita è più che adeguata per SQL Data Warehouse. È quindi possibile scegliere l'edizione più adatta alle proprie esigenze.

<a href="https://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5" target="blank">Ottenere Visual Studio 2013</a>

### Aggiornare Visual Studio 2013 ###
Se Visual Studio 2013 è già installato, non è necessario installarlo di nuovo. Per assicurarsi che sia aggiornato, è sufficiente eseguire i passaggi seguenti. È possibile procedere al passaggio successivo.

1. Aprire Visual Studio 2013.
2. Scegliere "Estensioni e aggiornamenti" dal menu "Strumenti".
3. Nel controllo struttura ad albero passare ad "Aggiornamenti" e "Aggiornamenti del prodotto".
4. Se non sono disponibili aggiornamenti, è possibile chiudere la finestra "Estensioni e aggiornamenti" e passare all'attività successiva di questa Guida introduttiva.

Se tuttavia è disponibile un aggiornamento per Visual Studio, fare clic su Aggiorna. Verrà avviato il download dell'aggiornamento più recente per Visual Studio 2013.

Prima di continuare, chiudere la finestra "Estensioni e aggiornamenti" e chiudere Visual Studio 2013.

5. Fare clic su "Esegui" per installare l'aggiornamento più recente per Visual Studio 2013.

6. Accettare le condizioni di licenza e fare clic su Installa, accettando eventuali richieste relative al Controllo dell'account utente.

7. Fare clic su "Avvia" per completare l'installazione.

L'aggiornamento di Visual Studio 2013 è stato completato.

### Aggiornare SSDT 
> [AZURE.IMPORTANT]L’SQL Data Warehouse richiede almeno la versione di anteprima di SSDT 12.0.50623 o versione successiva.

I tecnici che si occupano di SSDT aggiornano regolarmente il plug-in con le nuove funzionalità e ogni tanto sarà quindi necessario applicare gli aggiornamenti. Si tratta comunque di un processo molto semplice. Per verificare se è necessario aggiornare SSDT, eseguire i passaggi seguenti:

1. Aprire Visual Studio 2013.  
2. Scegliere "Estensioni e aggiornamenti" dal menu "Strumenti".
3. Nel controllo struttura ad albero passare ad "Aggiornamenti" e "Aggiornamenti del prodotto".
4. Se non sono disponibili aggiornamenti, è possibile chiudere la finestra "Estensioni e aggiornamenti" e passare all'attività successiva di questa Guida introduttiva.

Se tuttavia è disponibile un aggiornamento definito "Aggiornamento Microsoft SQL Server per strumenti di database", fare clic su Aggiorna.

Verrà avviato il download della versione più recente di SSDT. La figura seguente mostra il file SSDTSetup.exe in Download Manager di Internet Explorer.

5. Fare clic su "Esegui" per installare la versione più recente di SSDT.

6. Accettare le condizioni di licenza e fare clic su Installa.

7. Fare clic su "Chiudi" per completare l'aggiornamento di SSDT.

8. Chiudere la finestra "Estensioni e aggiornamenti".

Sul desktop è ora disponibile una versione aggiornata di Visual Studio 2013 con un'estensione SSDT aggiornata.

> [AZURE.NOTE]È attualmente consigliabile usare l'[anteprima di SSDT per Visual Studio 2013 versione 12.0.50623 o successive](http://go.microsoft.com/fwlink/?LinkID=616714&clcid=0x409)

## Connettersi a Visual Studio 2013
Se si esegue la versione desiderata di Visual Studio, sarà possibile connettersi all'istanza di SQL Data Warehouse in due modi diversi:

### Da Visual Studio
Per stabilire la connessione è sufficiente aprire Esplora oggetti di SQL Server e specificare le informazioni di connessione.

1. Aprire Visual Studio.
2. Scegliere "Esplora oggetti di SQL Server" dal menu "Visualizza".

> [AZURE.NOTE]Assicurarsi di selezionare Esplora oggetti di SQL Server, ***non*** Esplora server. I nomi sono simili, ma si tratta di controlli molto diversi. Si trovano uno vicino all'altro, quindi occorre assicurarsi di avere selezionato il controllo corretto.

È ora possibile visualizzare Esplora oggetti di SQL Server:


3. Fare clic su "Aggiungi server" in Esplora oggetti di SQL Server, come evidenziato nella figura seguente:

4. Popolare la finestra di dialogo Connetti al Server con i valori scelti durante la creazione del server logico. Inoltre, fare clic sul pulsante Opzioni e specificare il database a cui si desidera connettersi (l'istanza di SQL Data Warehouse) prima della connessione.

È anche possibile selezionare la casella "Memorizza password", se si vuole, che permette di risparmiare tempo. Occorre però ricordare che consente a chiunque possa accedere fisicamente al profilo di eseguire query usando questo account.

> [AZURE.NOTE]Si tenga presente che il nome del server deve essere completo. Il valore di Nome server deve quindi essere conforme a questa convenzione: ***NomeServer***.database.windows.net, dove ***NomeServer*** è il nome assegnato al server logico.

Dopo avere specificato tutte le credenziali necessarie, fare clic su Connetti.

La connessione è stata completata e il server logico SQL Data Warehouse è stato registrato in Esplora oggetti di SQL Server.
    
### Nel portale di Azure
Passare a Visual Studio direttamente dal portale di Azure.

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).
2. Selezionare l'istanza desiderata di SQL DW nel pannello "Sfoglia". 
3. Nella parte superiore del pannello SQL DW selezionare "Apri in Visual...".
4. Se il firewall non è stato configurato con l'indirizzo IP del computer client, selezionare "Configurare il firewall".
	1. Immettere i valori per "Nome regola", "Indirizzo IP iniziale" e "Indirizzo IP finale". 
	2. Fare clic su "Salva" nella parte superiore del pannello.   
5. Fare clic su "Apri in Visual Studio". 
6. Visual Studio verrà aperto e verranno richieste le credenziali. 
7. Dopo l'immissione delle credenziali e la connessione, il server e le istanze di DW verranno visualizzati nel pannello di Esplora oggetti di SQL Server.  

## Connettersi a SQL Data Warehouse con sqlcmd

È anche possibile connettersi a SQL DW con l'utilità del prompt dei comandi [sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx) inclusa in SQL Server o con [Microsoft Command Line Utilities 11 for SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501). L'utilità sqlcmd permette di immettere istruzioni Transact-SQL, procedure di sistema e file script al prompt dei comandi.

Per connettersi a un'istanza specifica di SQL DW quando si usa sqlcmd, sarà necessario aprire il prompt dei comandi e immettere *sqlcmd* seguito dalla stringa di connessione per il database di SQL DW. La stringa di connessione deve includere i parametri seguenti:

+ **Utente (-U):** utente del server nel formato `<`User`>`@`<`Server Name`>`.database.windows.net
+ **Password (-P):** password associata all'utente
+ **Server (-S):** server nel formato `<`Server Name`>`.database.windows.net
+ **Database (-D):** nome del database 
+ **Abilita identificatori delimitati (-I):** gli identificatori delimitati devono essere abilitati per consentire la connessione a un'istanza di SQL DW. 

Per connettersi a un'istanza di SQL DW, è quindi necessario immettere quanto segue:

```
C:\>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
```

Dopo la connessione sarà possibile eseguire qualsiasi istruzione Transact-SQL supportata nell'istanza. Ad esempio, l'istruzione seguente usa l'istruzione [CREATE TABLE](https://msdn.microsoft.com/library/azure/dn268335.aspx) per creare una nuova tabella.

```
C:\>sqlcmd -U <User>@<Server Name>.database.windows.net -P <Password> -S <Server Name>.database.windows.net -d <Database> -I
1> CREATE TABLE table1 (Col1 int, Col2 varchar(20));
2> GO
3> QUIT
```
	
Per altre informazioni su sqlcmd, vedere la [documentazione su sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).

<!--NOTE: SQL DB does not support the -z/-Z parameters for changing users password with SQLCMD.  Do we support this? -->

## Esecuzione di query di esempio ##

Dopo avere registrato il server, è possibile scrivere una query.

1. Fare clic sul database utente in SSDT.

2. Fare clic su Nuova query.

   Viene aperta una nuova finestra.

3. Scrivere una query

	Immettere il codice seguente nella finestra della query:

	```
	SELECT  COUNT(*)
	FROM    sys.tables;
	```

4. Eseguire la query

	Per eseguire la query, fare clic sulla freccia verde seguente oppure usare il collegamento seguente `CTRL`+`SHIFT`+`F5`:

## Passaggi successivi ##
[Load sample data.]: ./sql-data-warehouse-get-started-load-samples.md
[Start developing code]: ./articles/sql-data-warehouse-overview-develop/

<!---HONumber=August15_HO6-->