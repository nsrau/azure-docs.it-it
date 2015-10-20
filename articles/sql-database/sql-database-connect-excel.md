<properties
	pageTitle="Connettersi a un database SQL di Azure con Excel"
	description="Foglio di calcolo Excel al Database SQL Azure per l'esplorazione dei dati e creazione di report."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="joseidz"/>


# Connettersi a un database SQL di Azure con Excel

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Questo articolo illustra come connettere Excel a un database SQL di Azure e creare un report sui dati nel database. È necessario prima di tutto un database SQL. Se non ne è disponibile uno, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md) per ottenere un database con dati di esempio operativi in pochi minuti. Questo articolo si basa sui diti di esempio di quell'articolo, ma si può seguire una procedura simile con dati personalizzati.

Sarà necessaria anche una copia di Excel. Questo articolo usa [Microsoft Excel 2016](https://products.office.com/it-IT/).

## Connettersi e creare un report

1.	Aprire Excel e creare una nuova cartella di lavoro o aprire la cartella di lavoro a cui connettersi.

2.	Nella barra dei menu nella parte superiore della pagina fare clic su **Dati**, fare clic su **Da altre origini** e quindi su **Da SQL Server**.
	
	![Selezionare l'origine dati](./media/sql-database-connect-excel/excel_data_source.png)

	Si apre la Connessione guidata dati.

3.	Nella finestra di dialogo**Connessione al server di database** digitare il **Nome server** che ospita il server logico da connettere nel formato **<*nomeserver*>.database.windows.net**. Ad esempio, **adventureserver.database.windows.net**.

4.	Nella sezione **Credenziali di accesso** fare clic su **Usa nome utente e password seguenti**, digitare il **Nome utente** e la **Password** configurati per il server di database SQL quando è stato creato e quindi fare clic su **Avanti**.

	> [AZURE.TIP]Entrambi i componenti aggiuntivi [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) e [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) per Excel presentano esperienze simili.

5. Nella finestra di dialogo **Seleziona database e tabella** selezionare il database **AdventureWorks** dal menu a discesa e selezionare **vGetAllCategories** dall'elenco di tabelle e visualizzazioni, quindi fare clic su **Avanti**.

	![Selezionare database e tabella][5]

6. Nella finestra di dialogo **Salva file di connessione dati e chiudi** fare clic su **Fine**.

7. Nella finestra di dialogo**Importa dati** selezionare **Grafico pivot** e fare clic su **OK**.

	![Selezionare l'importazione dei dati][2]

8. Nella finestra di dialogo **Campi grafico pivot** selezionare la configurazione seguente per creare un report per il conteggio dei prodotti per categoria.

	![Configurazione][3]

	Esito positivo è simile al seguente:

	![esito positivo][4]

## Passaggi successivi

Per gli sviluppatori di software come servizio (SaaS), vedere [Pool di database elastici](sql-database-elastic-pool.md) per altre informazioni. È possibile gestire con facilità grandi raccolte di database con [Processi di database elastici](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=Oct15_HO3-->