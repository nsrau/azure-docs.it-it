
<properties
   pageTitle="Esportare un database SQL Server in un file BACPAC mediante SQL Server Management Studio | Microsoft Azure"
   description="Database SQL di Microsoft Azure, migrazione del database, esportazione del database, esportazione del file BACPAC, procedura guidata di esportazione dell’applicazione livello dati"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# Esportare un database SQL Server in un file BACPAC mediante SQL Server Management Studio

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
In questo articolo viene illustrato come esportare un database SQL Server in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la procedura guidata di esportazione dell’applicazione livello dati in SQL Server Management Studio.

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

	 > [AZURE.IMPORTANT] È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Fare clic con il pulsante destro del mouse sul database di origine in Esplora oggetti, scegliere **Attività** e quindi fare clic su **Esporta l'applicazione livello dati**.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Nell'esportazione guidata configurare l'esportazione per salvare il file BACPAC in un percorso sul disco locale o in un BLOB di Azure. Il file BACPAC esportato include sempre lo schema di database completo e, per impostazione predefinita, i dati di tutte le tabelle. Se si desidera escludere i dati da alcune o tutte le tabelle, utilizzare la scheda Avanzate. È ad esempio possibile scegliere di esportare solo i dati per le tabelle di riferimento, invece che da tutte le tabelle.

***Importante***: quando si esporta un file con estensione BACPAC in un archivio BLOB di Azure, usare l'archiviazione standard. L'importazione di un file BACPAC dall'archiviazione Premium non è supportata.

	![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


## Passaggi successivi

- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importare un file BACPAC nel database SQL di Azure tramite SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importare un file BACPAC nel database SQL di Azure tramite SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importare un file BACPAC nel database SQL di Azure tramite il portale di Azure](sql-database-import.md)
- [Importare un file BACPAC nel database SQL di Azure tramite PowerShell](sql-database-import-powershell.md)

## Risorse aggiuntive

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md) (Funzionalità di Transact-SQL parzialmente supportate o non supportate)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0817_2016-->