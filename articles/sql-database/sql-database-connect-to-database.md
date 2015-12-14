<properties
   pageTitle="Informazioni su come connettersi a un database SQL di Azure tramite SSMS | Microsoft Azure"
   description="Informazioni su come connettersi a un database SQL di Azure tramite SQL Server Management Studio."
   services="sql-database"
   documentationCenter=""
   authors="sidneyh"
   manager="jeffreyg"
   editor=""
   tags=""/>
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2015"
   ms.author="sidneyh"/>

# Connettersi con SQL Server Management Studio (SSMS)

Utilizzare i seguenti passaggi per installare e interrogare il database SQL tramite Server Management Studio (SSMS) di SQL.

## Prerequisiti

* SQL Server Management Studio (SSMS) - per scaricare la versione più recente di SSMS, vedere [Scaricare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
* Un database di esempio AdventureWorks come descritto in[Introduzione al database SQL di Microsoft Azure](sql-database-get-started.md).


## Ottenere il nome completo del server SQL di Azure

Per connettersi al database è necessario il nome completo del server (**** servername**.database.windows.net*) che contiene il database a cui si desidera connettersi.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al database a cui si desidera connettersi.
3. Individuare il nome completo del server:

    ![nome del server completo][6]

    Utilizzare il nome completo del server nel passaggio 3 seguente.



## Connettersi al database SQL

1. Aprire SQL Server Management Studio.
2. Fare clic su **Connetti** > **Motore di Database...**

    ![Connetti > Motore di database][7]

2. Nella finestra **Connetti al server**, nella casella **Nome server**, digitare il nome del server nel formato *&lt;servername>*.**database.windows.net**
3. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.
4. Immettere il **Nome di accesso** e la **Password**specificati quando è stato creato il server di Database SQL e fare clic su **Connetti**.

	![Finestra di dialogo Connetti al server][2]



### Se la connessione ha esito negativo
Assicurarsi che il firewall del server logico che è stato creato consenta le connessioni dal computer locale. Per altre informazioni, vedere [Procedura: configurare le impostazioni del firewall nel database SQL di Azure](sql-database-configure-firewall-settings.md).

## Eseguire query di esempio

1. In **Esplora oggetti** passare al database **AdventureWorks**.
2. Fare clic con il pulsante destro del mouse sul database e selezionare **Nuova Query**.

	![Nuova query][4]

3. Nella finestra della query, copiare e incollare il codice seguente:

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Fare clic sul pulsante **Esegui**. Nella schermata seguente viene illustrata una query con esito positivo.

	![Operazione riuscita][5]




## Passaggi successivi
Per creare o gestire i database è possibile usare istruzioni Transact-SQL. Per ulteriori informazioni, vedere [CREARE DATABASE (Database di SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Gestione di database SQL di Azure tramite SQL Server Management Studio](sql-database-manage-azure-ssms.md). È anche possibile registrare eventi nell'archiviazione di Azure. Vedere [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md) per ulteriori informazioni.

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=AcomDC_1203_2015-->