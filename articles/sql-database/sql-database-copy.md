<properties
	pageTitle="Copiare un database SQL di Azure | Microsoft Azure"
	description="Creare una copia di un database SQL di Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="01/20/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copiare un database SQL di Azure

**Database singolo**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

La procedura seguente illustra come copiare un database SQL con il [portale di Azure](https://portal.azure.com). L'operazione di copia del database crea un nuovo database SQL. La copia è un backup snapshot del database creato nello stesso server o in un server diverso.

> [AZURE.NOTE] Il database SQL di Azure crea automaticamente e conserva i backup per ogni database dell'utente che è possibile ripristinare. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

Al completamento del processo di copia, il nuovo database è completamente funzionante, indipendente dal database di origine. Al completamento della copia, il nuovo database è coerente da un punto di vista transazionale con il database di origine. Il livello di servizio e il livello di prestazioni (piano tariffario) della copia del database sono gli stessi del database di origine. Al termine del processo di copia, la copia diventa un database indipendente e completamente funzionante. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente.


Quando si copia un database nello stesso server logico, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database (DBO) nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.


Per copiare un database SQL è necessario quanto segue:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un database SQL da copiare. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).



## Copiare il database SQL

Aprire il pannello del database SQL per il database che si desidera copiare:

1.	Accedere al [portale di Azure](https://portal.azure.com).
2.	Accedere al database che si desidera copiare: Sfoglia > Database SQL
3.	Nel pannello del database SQL fare clic su **Copia** per aprire il pannello **Copia**:

    ![copia database][1]

1.  Immettere un nome per la copia del database. È presente un nome predefinito, ma è possibile modificarlo.
2.  Selezionare un **server di destinazione**. Il server di destinazione è il server in cui verrà creata la copia del database. È possibile creare un nuovo server o selezionare un server esistente dall'elenco.
3.  Fare clic su **OK** per avviare il processo di copia.

    ![nome e server del database][2]




## Monitorare lo stato dell'operazione di copia

2.	Dopo aver avviato il processo di copia, fare clic sulla notifica del portale per i dettagli.


    ![notifica][3]

 
    ![monitoraggio][4]





## Verificare che il database sia disponibile nel server

- Fare clic su **SFOGLIA** > **Database SQL** e verificare che il nuovo database sia **Online**.



## Passaggi successivi

- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)



## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=AcomDC_0211_2016-->