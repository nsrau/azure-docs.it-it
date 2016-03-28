<properties
   pageTitle="Risolvere i problemi di compatibilità del database del server SQL prima di effettuare la migrazione al database SQL"
   description="Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure, SSDT"
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Risolvere i problemi di compatibilità del database del server SQL prima di effettuare la migrazione al database SQL

Se si determina che il database del server SQL di origine non è compatibile, sarà possibile risolvere in diversi modi i problemi di compatibilità identificati in precedenza.

> [AZURE.SELECTOR]
- Usare [Migrazione guidata SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usare [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usare [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## Utilizzare SQL Server Data Tools per Visual Studio.

Utilizzare SQL Server Data Tools per Visual Studio ("SSDT") per importare lo schema del database in un progetto database di Visual Studio per l'analisi. Per eseguire l’analisi, specificare Database SQL versione 12 come piattaforma di destinazione per il progetto, quindi compilare il progetto. Se la compilazione ha esito positivo, il database è compatibile. Se la compilazione non viene eseguita correttamente, è possibile risolvere gli errori in SSDT (o uno degli altri strumenti descritti in questo argomento). Una volta creato il progetto, è possibile pubblicarlo nuovamente come una copia del database di origine e quindi utilizzare la funzione di confronto di dati in SSDT per copiare i dati dal database di origine al database compatibile alla versione 12 di SQL Azure. È quindi possibile migrare il database aggiornato. Per utilizzare questa opzione, scaricare la [versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagramma di migrazione di VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Se è necessario eseguire la migrazione solo dello schema, è possibile pubblicarlo direttamente da Visual Studio nel database SQL di Azure. Utilizzare questo metodo quando lo schema del database richiede altre modifiche che è possibile gestire solo attraverso la migrazione guidata.

## Passaggio successivo: selezionare il metodo di migrazione ed eseguire la migrazione

[Selezionare il metodo di migrazione](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_0316_2016-->