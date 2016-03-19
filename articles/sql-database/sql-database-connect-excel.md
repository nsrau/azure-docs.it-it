<properties
	pageTitle="Connettere Excel al database SQL | Microsoft Azure"
	description="Informazioni su come connettere Microsoft Excel al database SQL di Azure nel cloud. Importare i dati in Excel per creare report ed esplorare i dati."
	services="sql-database"
	keywords="connettere Excel a SQL, importare i dati in Excel"
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
	ms.date="02/04/2016"
	ms.author="joseidz"/>


# Connettere Excel a un database SQL di Azure e creare un report 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Informazioni su come connettere Excel a un database SQL per importare i dati in Excel e creare un report in base a tali dati.

È necessario prima di tutto un database SQL. Se non ne è già stato creato uno, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md) per ottenere un database con dati di esempio operativo in pochi minuti. Questo articolo descrive come importare i dati di esempio in Excel, ma è anche possibile seguire la procedura usando dati personalizzati.

Sarà necessaria anche una copia di Excel. In questa esercitazione viene usato [Microsoft Excel 2016](https://products.office.com/it-IT/).

## Connettere Excel a un database SQL e creare un report

1.	Per connettere Excel a un database SQL, aprire Excel e quindi creare una nuova cartella di lavoro. In alternativa, aprire una cartella di lavoro di Excel esistente.

2.	Nella barra dei menu nella parte superiore della pagina fare clic su **Dati**, quindi su **Da altre origini** e infine su **Da SQL Server**.

	![Selezione dell'origine dati: connettere Excel al database SQL.](./media/sql-database-connect-excel/excel_data_source.png)

	Si apre la Connessione guidata dati.

3.	Nella finestra di dialogo **Connessione al server di database** digitare il **Nome del server** che ospita il server logico a cui si vuole connettersi nel formato **<*nomeserver*>.database.windows.net**. Ad esempio, **adventureserver.database.windows.net**.

4.	Nella sezione **Credenziali di accesso** fare clic su **Usa nome utente e password seguenti**, digitare il **Nome utente** e la **Password** configurati per il server di database SQL quando è stato creato e quindi fare clic su **Avanti**.

	> [AZURE.TIP] Entrambi i componenti aggiuntivi [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) e [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) per Excel presentano esperienze simili.

5. Nella finestra di dialogo **Seleziona database e tabella** selezionare il database **AdventureWorks** dal menu a discesa, selezionare **vGetAllCategories** dall'elenco di tabelle e visualizzazioni e quindi fare clic su **Avanti**.

	![Selezionare un database e una tabella.][5]

6. Nella finestra di dialogo **Salva file di connessione dati e chiudi** fare clic su **Fine**.

7. Nella finestra di dialogo **Importa dati** selezionare **Grafico pivot** e fare clic su **OK**.

	![Importare i dati in Excel: selezionare Grafico pivot nella finestra di dialogo Importa dati.][2]

8. Nella finestra di dialogo **Campi grafico pivot** selezionare la configurazione seguente per creare un report per il conteggio dei prodotti per categoria.

	![Configurare un report di database.][3]

	Se l'operazione riesce, l'aspetto sarà simile al seguente:

	![Operazione riuscita: Excel è connesso al database SQL.][4]

## Passaggi successivi

Per gli sviluppatori di software come servizio (SaaS), vedere [Pool di database elastici](sql-database-elastic-pool.md) per altre informazioni. I [processi di database elastici](sql-database-elastic-jobs-overview.md) consentono di gestire con facilità raccolte di database di grandi dimensioni.

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=AcomDC_0204_2016-->