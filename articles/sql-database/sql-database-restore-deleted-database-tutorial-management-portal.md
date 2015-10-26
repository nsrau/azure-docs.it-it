<properties 
   pageTitle="Ripristinare un database SQL di Azure nel portale di Azure" 
   description="Database SQL di Microsoft Azure, ripristinare un database eliminato, recuperare un database eliminato, Portale di gestione di Azure, portale di Azure" 
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
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal"/>

# Ripristinare un database SQL di Azure nel portale di Azure

> [AZURE.SELECTOR]
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## Panoramica

In questa esercitazione viene illustrato come ripristinare un database SQL di Azure eliminato nel [portale di Azure](http://manage.windowsazure.com). È possibile ripristinare un database che era stato eliminato durante il periodo di conservazione al punto in cui è stato eliminato. Il periodo di conservazione viene determinato dal livello di servizio del database.

Quando si ripristina un database SQL di Azure eliminato viene creato un nuovo database. Il servizio seleziona automaticamente il livello di servizio in base al backup utilizzato nel punto di ripristino. Accertarsi di disporre di una quota sul server logico per creare un altro database. Se si desidera richiedere un aumento della quota, contattare il [supporto Azure](http://azure.microsoft.com/support/options/).

## Restrizioni e sicurezza

* Il ripristino di un database SQL di Azure eliminato è abilitato per tutti i livelli di servizio Basic, Standard e Premium. 

* I periodi di conservazione dei backup sono: Basic, 7 giorni; Standard, 14 giorni; Premium, 35 giorni.

* Solo l'amministratore o il co-amministratore della sottoscrizione può inviare una richiesta di ripristino.

* L'accesso principale a livello di server sarà il proprietario del database ripristinato.
 
* I livelli di servizio Web and Business Edition non supportano il ripristino di un database SQL eliminato.
 
	* Se si dispone di un database Web o Business Edition, è possibile utilizzare la copia del database per ottenere una copia transazionale coerente del database e quindi esportare il database copiato in un account di archiviazione di Microsoft Azure. Per informazioni dettagliate, vedere [Procedura: utilizzare la copia di database (database SQL di Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) e [Procedura: utilizzare il servizio di importazione ed esportazione nel database SQL di Azure](http://msdn.microsoft.com/library/azure/hh335292.aspx).
	* Web Edition e Business Edition verranno ritirati a settembre 2015. Per ulteriori informazioni, vedere [Domande frequenti sull'interruzione delle edizioni Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Procedura: ripristinare un database SQL di Azure eliminato nel portale di Azure

> [AZURE.VIDEO restore-a-deleted-sql-database]

1. Accedere al [portale di Azure](http://manage.windowsazure.com) mediante il proprio account Microsoft.

2. Nel riquadro di navigazione a sinistra, fare clic su **DATABASE SQL**.

3. Nella parte superiore della pagina, fare clic su **DATABASE ELIMINATI**. Viene visualizzato un elenco **DATABASE ELIMINATI RIPRISTINABILI**.

4. Fare clic sul database che si desidera ripristinare.

6. In fondo alla pagina, nella barra dei comandi, fare clic su **Ripristina**. In tal modo viene aperta la finestra di dialogo **Specifica impostazioni di ripristino**.

7. Scegliere un **NOME DATABASE**. Per impostazione predefinita, viene scelto automaticamente un nome di database, che tuttavia può essere modificato se lo si desidera.

	* Un database eliminato può essere ripristinato solo nel server dal quale è stato eliminato e al punto in cui è stato eliminato.   

8. Fare clic sul segno di spunta per inviare la richiesta di ripristino.

Il completamento di un ripristino può richiedere del tempo. Per monitorare lo stato del ripristino, fare clic sull'icona dello stato in basso a destra nella pagina della barra dei comandi, quindi fare clic su **DETTAGLI**.

## Passaggi successivi

Per altre informazioni, vedere quanto segue:

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

<!---HONumber=Oct15_HO3-->