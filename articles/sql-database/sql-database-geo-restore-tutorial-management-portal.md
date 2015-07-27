<properties 
   pageTitle="Ripristinare un database SQL di Azure mediante Ripristino geografico nel portale di Azure" 
   description="Ripristino geografico, Database SQL di Microsoft Azure, ripristinare un database, recuperare un database, Portale di gestione di Azure, portale Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="03/18/2015"
   ms.author="elfish; v-romcal"/>

# Ripristinare un database SQL di Azure mediante Ripristino geografico nel portale di Azure

> [AZURE.SELECTOR]
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## Panoramica

In questa esercitazione viene illustrato come ripristinare un database SQL di Azure mediante Ripristino geografico nel [portale di Azure](http://manage.windowsazure.com). Ripristino geografico è la protezione da ripristino di emergenza inclusa per tutti i livelli di servizio Basic, Standard e Premium dei database SQL di Azure.

## Restrizioni e sicurezza

* Ripristino geografico è abilitato per tutti i livelli di servizio Basic, Standard e Premium.

* I periodi di conservazione dei backup sono: Basic, 7 giorni; Standard, 14 giorni; Premium, 35 giorni.

* Solo l'amministratore o il co-amministratore della sottoscrizione può inviare una richiesta di ripristino.

* L'accesso principale a livello di server sarà il proprietario del database ripristinato.

* I livelli di servizio Web e Business Edition non supportano Ripristino geografico.
 
	* Se si dispone di un database Web o Business Edition, è possibile utilizzare la copia del database per ottenere una copia transazionale coerente del database e quindi esportare il database copiato in un account di archiviazione di Microsoft Azure. Per informazioni dettagliate, vedere [Procedura: utilizzare la copia di database (database SQL di Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) e [Procedura: utilizzare il servizio di importazione ed esportazione nel database SQL di Azure](http://msdn.microsoft.com/library/azure/hh335292.aspx).

	* Web Edition e Business Edition verranno ritirati a settembre 2015. Per ulteriori informazioni, vedere [Domande frequenti sull'interruzione delle edizioni Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Procedura: ripristinare un database SQL di Azure mediante Ripristino geografico nel portale di Azure

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore]

1. Accedere al [portale di Azure](http://manage.windowsazure.com) utilizzando l'account Microsoft e selezionare **Database SQL**.

2. Nel riquadro di navigazione a sinistra, fare clic su **DATABASE SQL**.

3. Nella parte superiore della pagina, fare clic su **SERVER**.

4. Nell'elenco **SERVER**, fare clic sul server **Danneggiato**.

4. Nella parte superiore della pagina, fare clic su **BACKUP**.

5. Fare clic sul database che si desidera ripristinare.

6. In fondo alla pagina, nella barra dei comandi, fare clic su **Ripristina**. In tal modo viene aperta la finestra di dialogo **Specifica impostazioni di ripristino**.

7. Scegliere il **NOME DATABASE** e il **SERVER DI DESTINAZIONE** in cui si desidera ripristinare il database. Per impostazione predefinita, viene scelto automaticamente un nome di database, che tuttavia può essere modificato se lo si desidera.

9. Fare clic sul segno di spunta per inviare la richiesta di ripristino.

Il completamento di un ripristino può richiedere del tempo. Per monitorare lo stato del ripristino, fare clic sull'icona dello stato in basso a destra nella pagina della barra dei comandi, quindi fare clic su **DETTAGLI**.

## Passaggi successivi

Per altre informazioni, vedere quanto segue:

[Ripristinare un database SQL di Azure mediante il ripristino temporizzato nel portale di Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Ripristinare un database SQL di Azure nel portale di Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Ripristino geografico del database SQL di Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

<!---HONumber=July15_HO3-->