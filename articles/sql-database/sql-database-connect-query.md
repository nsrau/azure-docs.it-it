<properties
	pageTitle="Connettersi al database SQL con una query in C# | Microsoft Azure"
	description="Scrivere un programma in C# per eseguire query e connettersi al database SQL. Informazioni su indirizzi IP, stringhe di connessione, accesso sicuro e Visual Studio gratuito."
	services="sql-database"
	keywords="query di database in c#, query in c#, connettersi al database, SQL C#"
	documentationCenter=""
	authors="MightyPen"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/17/2016"
	ms.author="annemill"/>


# Connettersi a un database SQL con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Informazioni su come connettersi a un database SQL di Azure in Visual Studio.

## Prerequisiti


Per connettersi a un database SQL usando Visual Studio, sono necessari gli elementi seguenti:


- Un account e una sottoscrizione di Azure. È possibile iscriversi per una [versione di prova gratuita](https://azure.microsoft.com/pricing/free-trial/).


- Un database **AdventureWorksLT** dimostrativo nel servizio database SQL di Azure.
 - È possibile [crearne uno](sql-database-get-started.md) in pochi minuti.


- Visual Studio 2013 Update 4 o versioni successive. Ora Microsoft fornisce Visual Studio Community *gratuitamente*.
 - [Download di Visual Studio Community](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Altre opzioni per la versione gratuita di Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - In alternativa, andare direttamente al [passaggio](#InstallVSForFree) corrispondente più avanti in questo argomento, che descrive come installare Visual Studio dal [portale di Azure](https://portal.azure.com/).


<a name="InstallVSForFree" id="InstallVSForFree"></a>

& nbsp;

## Passaggio 1: Installare Visual Studio Community gratuitamente


Per installare Visual Studio, è possibile:

- Installare Visual Studio Community gratuitamente passando alle pagine Web dedicate a Visual Studio in cui sono disponibili download gratuiti o altre opzioni.
- Accedere al [portale di Azure](https://portal.azure.com/) e passare direttamente alla pagina Web per il download, seguendo la procedura descritta di seguito.


### Visual Studio tramite il portale di Azure


1. Accedere al [portale di Azure](https://portal.azure.com/), http://portal.azure.com/.

2. Fare clic su *ESPLORA* TUTTO** > **Database SQL**. Verrà visualizzato un pannello in cui viene eseguita la ricerca dei database.

3. Nella casella di testo del filtro in alto digitare il nome del database **AdventureWorksLT**.

4. Quando viene visualizzata la riga corrispondente al database nel server, fare clic su di essa. Verrà visualizzato un pannello per il database.

5. Per praticità, ridurre a icona i pannelli precedenti facendo clic sul controllo corrispondente.

6. Fare clic sul pulsante **Apri in Visual Studio** in alto nel pannello del database. Verrà visualizzato un nuovo pannello per Visual Studio con i collegamenti alle pagine da cui è possibile eseguire l'installazione di Visual Studio.

	![Pulsante Apri in Visual Studio][20-OpenInVisualStudioButton]

7. Fare clic sul collegamento **Community (Gratuito)** o simile. Verrà aggiunta una nuova pagina Web.

8. Per installare Visual Studio, usare i collegamenti disponibili nella nuova pagina Web.

9. Al termine dell'installazione di Visual Studio, nel pannello **Apri in Visual Studio** fare clic sul pulsante **Apri in Visual Studio**. Verrà aperto Visual Studio.

10. Visual Studio richiederà la compilazione dei campi relativi alla stringa di connessione in una finestra di dialogo.
 - Scegliere **Autenticazione di SQL Server**, non **Autenticazione di Windows**.
 - Ricordare di specificare il database **AdventureWorksLT** in **Opzioni** > **Proprietà connessione** nella finestra di dialogo.

11. In **Esplora oggetti di SQL Server** espandere il nodo del database.


## Passaggio 2: Eseguire query di esempio

Dopo la connessione al server logico, è possibile connettersi a un database ed eseguire una query di esempio.

1. In **Esplora oggetti** passare a un database nel server per cui si ha l'autorizzazione, come il database di esempio **AdventureWorks**.
2. Fare clic con il pulsante destro del mouse sul database e selezionare **Nuova query**.

	![Nuova query. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)  

3. Nella finestra della query appena aperta, copiare e incollare il codice seguente:

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Fare clic sul pulsante **Esegui**. Nella schermata seguente viene illustrata una query con esito positivo.

	![Completamento della procedura. Connettersi al server di database SQL: SVisual Studio](./media/sql-database-connect-query-ssms/5-success.png)  

## Passaggi successivi

[Connettersi al database SQL tramite .NET (C#)](sql-database-develop-dotnet-simple.md)


<!-- Image references. -->  

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png

<!---HONumber=AcomDC_0817_2016-->