<properties
   pageTitle="Determinare la compatibilità del database SQL utilizzando SSMS"
   description="Database SQL di Microsoft Azure, migrazione del database, compatibilità database SQL, procedura guidata di esportazione dell’applicazione livello dati"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Determinare la compatibilità del database SQL utilizzando SSMS

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
 
In questo articolo viene illustrato come determinare se un database del server SQL è compatibile per eseguire la migrazione al database SQL tramite la procedura guidata di esportazione dell’applicazione livello dati in SQL Server Management Studio.

## Utilizzare SQL Server Management Studio

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

 	 >[AZURE.IMPORTANT]È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.
3. Fare clic con il pulsante destro del mouse sul database di origine in Esplora oggetti, scegliere **Attività** e quindi fare clic su **Esporta l'applicazione livello dati**.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Nell'esportazione guidata, fare clic su **Avanti** e quindi nella scheda **Impostazioni** configurare l'esportazione per salvare il file BACPAC su un percorso disco locale o in un BLOB di Azure. Un file BACPAC verrà salvato solo se non sono presenti problemi di compatibilità del database. Eventuali problemi di compatibilità verranno visualizzati nella console.

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Fare clic sulla scheda **Avanzate** e deselezionare la casella di controllo **Seleziona tutto** per evitare l'esportazione dei dati. In questa fase l'obiettivo principale consiste nella verifica della compatibilità.

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Fare clic su **Avanti** e quindi su **Fine**. Eventuali problemi di compatibilità verranno visualizzati dopo la convalida dello schema da parte della procedura guidata.

	![Esportazione impostazioni](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Se non vengono visualizzati errori, il database sarà compatibile e sarà possibile iniziare la migrazione. Se sono presenti errori, sarà necessario risolverli. Per visualizzare gli errori, fare clic su **Errore** per **Convalida schema**. ![Impostazioni di esportazione](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Se il file *.BACPAC è stato generato, il database è compatibile con il database SQL ed è possibile eseguire la migrazione.

## Passaggio successivo: risolvere i problemi di compatibilità, se presenti

[Risolvere i problemi di compatibilità del database](sql-database-cloud-migrate-fix-compatibility-issues.md), se presenti.

<!---HONumber=AcomDC_1223_2015-->