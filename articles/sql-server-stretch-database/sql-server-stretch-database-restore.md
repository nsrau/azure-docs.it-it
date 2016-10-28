<properties
	pageTitle="Eseguire il ripristino di database con estensione abilitata | Microsoft Azure"
	description="Informazioni su come eseguire il ripristino di database con estensione abilitata."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="douglasl"/>

# Ripristinare database con estensione abilitata

Se necessario, ripristinare un database di cui è stato eseguito un backup per il recupero in caso di anomalie, errori ed emergenze.

Per altre informazioni sul backup, vedere [Eseguire backup e ripristino di database con estensione abilitata](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] Il backup è solo una parte di una soluzione completa di continuità aziendale e a disponibilità elevata. Per altre informazioni sulla disponibilità elevata, vedere [Soluzioni a disponibilità elevata (SQL Server)](https://msdn.microsoft.com/library/ms190202.aspx).

## Ripristinare i dati di SQL Server
Per il recupero in caso di danneggiamenti o errori hardware, ripristinare il database SQL Server con estensione abilitata da un backup. È possibile continuare a impiegare i metodi di ripristino SQL Server attualmente in uso. Per altre informazioni, vedere [Panoramica del ripristino e del recupero (SQL Server)](https://msdn.microsoft.com/library/ms191253.aspx).

Dopo aver ripristinato il database SQL Server, è necessario eseguire la stored procedure **sys.sp\_rda\_reauthorize\_db** per ristabilire la connessione tra il database SQL Server con estensione abilitata e il database di Azure remoto. Per altre informazioni, vedere [Ripristinare la connessione tra il database SQL Server e il database di Azure remoto](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## Ripristinare i dati di Azure remoti

### Ripristinare un database di Azure attivo
Il servizio Estensione database di SQL Server su Azure esegue gli snapshot di tutti i dati dinamici almeno ogni 8 ore usando gli snapshot di Archiviazione di Azure. Tali snapshot vengono conservati per 7 giorni. È quindi possibile ripristinare i dati tornando indietro almeno a uno dei 21 momenti specifici degli ultimi 7 giorni e arrivando fino all'ora in cui è stato acquisito l'ultimo snapshot.

Per ripristinare un database di Azure attivo da un punto precedente nel tempo tramite il portale di Azure, eseguire le operazioni seguenti.

1. Accedere al portale di Azure.
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Database SQL**.
3. Individuare e selezionare il database.
4. Nella parte superiore del pannello del database fare clic su **Ripristina**.
5. Specificare un nuovo **Nome database**, quindi selezionare un **Punto di ripristino** e fare clic su **Crea**.
6. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**.

### Ripristinare un database di Azure eliminato
Prima dell'eliminazione del database, il servizio Estensione database di SQL Server su Azure esegue uno snapshot del database e lo conserva per 7 giorni. In questo caso, non mantiene gli snapshot dal database attivo. Ciò consente di ripristinare un database eliminato al momento dell'eliminazione.

Per ripristinare un database di Azure eliminato dal punto dell'eliminazione tramite il portale di Azure, eseguire le operazioni seguenti.

1. Accedere al portale di Azure.
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Server SQL**.
3. Individuare il server e selezionarlo.
4. Scorrere verso il basso fino a Operazioni nel pannello del server e fare clic sul riquadro **Database eliminati**.
5. Fare clic sul database eliminato che si desidera ripristinare.
5. Specificare un nuovo **Nome database** e fare clic su **Crea**.
6. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**.

## Ripristinare la connessione tra il database SQL Server e il database di Azure remoto

1.  Se si prevede di connettersi a un database di Azure ripristinato con un nome diverso o in un'area diversa, eseguire la stored procedure [sys.sp\_rda\_deauthorize\_db](https://msdn.microsoft.com/library/mt703716.aspx) per disconnettersi dal database di Azure precedente.

2.  Eseguire la stored procedure [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx) per riconnettere il database locale con estensione abilitata al database di Azure.

	-   Specificare le credenziali con ambito database come sysname o valore varchar (128). Non usare varchar(max). È possibile cercare il nome delle credenziali nella vista **sys.database\_scoped\_credentials**.

	-   Specificare se creare una copia dei dati remoti e connettersi alla copia (scelta consigliata).

    ```tsql  
    USE <Stretch-enabled database name>;
	GO
	EXEC sp_rda_reauthorize_db
	    @credential = N'<existing_database_scoped_credential_name>',
		@with_copy = 1 ;  
	GO
	```  

## Vedere anche

[Gestire e risolvere i problemi di Database Estensione](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Backup e ripristino di database SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0803_2016-->