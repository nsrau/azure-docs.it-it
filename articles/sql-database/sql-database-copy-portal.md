<properties
	pageTitle="Copiare un database SQL di Azure tramite il portale di Azure | Microsoft Azure"
	description="Creare una copia di un database SQL di Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/> 

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/> 



# Copiare un database SQL di Azure tramite il portale di Azure

> [AZURE.SELECTOR]
- [Panoramica](sql-database-copy.md)
- [Portale di Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

La procedura seguente illustra come copiare un database SQL tramite il [portale di Azure](https://portal.azure.com) nello stesso server o in un server diverso.

Per copiare un database SQL è necessario quanto segue:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione di Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Un database SQL da copiare. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).


## Copiare il database SQL

Aprire la pagina del database SQL per il database che si desidera copiare:

1.	Accedere al [portale di Azure](https://portal.azure.com).
2.	Fare clic su **Altri servizi** > **Database SQL**, e quindi fare clic sul database desiderato.
3.	Nella pagina del database SQL, fare clic su **Copia**:

    ![Database SQL](./media/sql-database-copy-portal/sql-database-copy.png) 

1.  Nella pagina **Copia** viene fornito un nome predefinito del database. È possibile digitare un nome diverso. Tutti i database in un server devono avere nomi univoci.
2.  Selezionare un **server di destinazione**. Il server di destinazione è il server in cui verrà creata la copia del database. È possibile copiare il database nello stesso server o su un altro server. È possibile creare un nuovo server o selezionare un server esistente dall'elenco.
3.  Dopo aver selezionato il **server di destinazione**, saranno abilitate le opzioni **Pool di database elastici** e **Piano tariffario**. Se il server dispone di un pool, è possibile copiarvi il database.
3.  Fare clic su **OK** per avviare il processo di copia.

    ![Database SQL](./media/sql-database-copy-portal/copy-page.png) 


## Monitorare lo stato dell'operazione di copia

- Dopo aver avviato il processo di copia, fare clic sulla notifica del portale per i dettagli.

    ![notifica][3] 
 
    ![monitoraggio][4] 


## Verificare che il database sia disponibile nel server

- Fare clic su **Altri servizi** > **Database SQL** e verificare che il nuovo database sia **Online**.


## Risolvere gli account di accesso

Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## Passaggi successivi

- Vedere [Copiare un database SQL di Azure](sql-database-copy.md) per una panoramica su come copiare un database SQL di Azure.
- Vedere [Copiare un database SQL di Azure con PowerShell](sql-database-copy-powershell.md) per copiare un database tramite PowerShell.
- Vedere [Copiare un database SQL di Azure con Transact-SQL](sql-database-copy-transact-sql.md) per copiare un database usando Transact-SQL.
- Vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso.



## Risorse aggiuntive

- [Gestire gli accessi](sql-database-manage-logins.md)
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references--> 
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

<!---HONumber=AcomDC_0921_2016-->