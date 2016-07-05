<properties
   pageTitle="Continuità aziendale cloud - Ripristino temporizzato - Database SQL | Microsoft Azure"
   description="Informazioni sul ripristino temporizzato, che consente di ripristinare un database SQL di Azure a un momento precedente (fino a 35 giorni)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/17/2016"
   ms.author="sstein"/>

# Panoramica: Ripristino temporizzato del database SQL

> [AZURE.SELECTOR]
- [Panoramica sulla continuità aziendale](sql-datbase-business-continuity.md)
- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Ripristinare un database eliminato](sql-database-restore-deleted-database.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)

La funzionalità di ripristino temporizzato consente di ripristinare un database esistente come database nuovo a un momento precedente nel tempo sullo stesso server logico, usando i [Backup automatici del database SQL](sql-database-automated-backups.md). Non è possibile sovrascrivere il database esistente. È possibile eseguire il ripristino a un punto precedente nel tempo usando il [portale di Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) o l'[API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Il database può essere ripristinato a qualsiasi livello di prestazioni o pool elastico. È necessario avere a disposizione una quota DTU sufficiente nel server o nel pool tenendo presente che il ripristino crea un nuovo database e che il livello di prestazioni e il livello di servizio del database ripristinato potrebbe essere diverso dallo stato corrente del database attivo. Al termine, il database ripristinato è un normale database online completamente accessibile alle tariffe normali in base al livello di prestazioni e al livello di servizio.

Per individuare quello meno recente, usare [Get Database](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*), che consente di ottenere il punto di ripristino più vecchio, senza replica geografica.

Se si sta ripristinando il database per scopi di recupero è possibile considerare il database ripristinato come una sostituzione per il database originale o usarlo per recuperare i dati e quindi aggiornare il database originale. Se il database ripristinato è da intendersi come una sostituzione per il database originale, è necessario verificare che il livello di prestazioni e/o livello di servizio sia appropriato e ridimensionare il database se necessario. È possibile rinominare il database originale e quindi assegnare al database ripristinato il nome originale usando il comando ALTER DATABASE in T-SQL.

Se si prevede di recuperare dati dal database ripristinato, sarà necessario scrivere ed eseguire separatamente qualsiasi script di ripristino dei dati sia necessario. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database sarà visibile nell'intero elenco dei database. Se si elimina il database durante il ripristino l'operazione verrà annullata e non verrà addebitata.

## Tempo di ripristino per un ripristino temporizzato

Il tempo impiegato per ripristinare un database dipende da molti fattori, tra cui la dimensione del database, il numero di log delle transazioni, il punto di ripristino selezionato e la quantità di attività che deve essere ripetuta per ricostituire lo stato nel punto selezionato. Per un database molto grande e/o attivo il ripristino potrebbe richiedere diverse ore. Per la maggior parte dei database, il ripristino richiede al massimo 12 ore.

## Confronto tra backup/ripristino e copia/esportazione/importazione

Il ripristino temporizzato è l'approccio consigliato per il ripristino dei database Basic, Standard e Premium dalla perdita o dal danneggiamento accidentale dei dati. Il backup e ripristino comporta costi inferiori, perché i backup non prevedono l'applicazione di alcuna tariffa a meno che non siano eccessivi, mentre verrà addebitata una tariffa per la copia del database, necessaria per garantire un'esportazione coerente delle transazioni e per l'archiviazione del file BACPAC. Inoltre, l'intervento dell'amministratore non è necessario, perché i backup sono automatici, mentre è necessario gestire o pianificare da sé le esportazioni, e offre un miglior RPO: è possibile ripristinare uno specifico stato precedente con una granularità molto più fine (un minuto) rispetto a quella normalmente impiegata per le operazioni di copia/esportazione/importazione. Infine, offre un miglior tempo di ripristino, perché il ripristino dai backup in genere è più veloce rispetto all'importazione, che implica passaggi di creazione dello schema, disabilitazione degli indici, caricamento dei dati e infine abilitazione degli indici per ogni singola tabella. La copia e l'esportazione continuano a essere la soluzione consigliata per l'archiviazione a lungo termine oltre il periodo di conservazione.


## Riepilogo

I backup automatici e il ripristino temporizzato proteggono i database dall'eliminazione o dal danneggiamento accidentale dei dati. Questa soluzione a costo zero e senza l'intervento dell'amministratore è disponibile con tutti i database SQL. Il backup e ripristino offre un miglioramento significativo rispetto alla soluzione alternativa di copia/esportazione/importazione per le esigenze di ripristino a breve termine. È consigliabile usare il ripristino temporizzato come parte della strategia di continuità aziendale e di usare l'esportazione solo se necessario per l'archiviazione a più lungo termine o a scopo di migrazione dei dati.


## Passaggi successivi

- Per istruzioni dettagliate su come eseguire il ripristino temporizzato nel portale di Azure, vedere l'articolo sul [ripristino temporizzato nel portale di Azure](sql-database-point-in-time-restore-portal.md).
- Per istruzioni dettagliate su come eseguire il ripristino temporizzato utilizzando PowerShell, vedere l'articolo sul [ripristino temporizzato tramite PowerShell](sql-database-point-in-time-restore-powershell.md).
- Per informazioni su come eseguire il ripristino temporizzato tramite l'API REST, vedere l'articolo sul [ripristino temporizzato mediante l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). ore.md)
- Per una spiegazione completa su come eseguire il ripristino dall'errore di un utente o di un'applicazione, vedere l'articolo sul [ripristino da errori dell'utente](sql-database-user-error-recovery.md).

## Risorse aggiuntive

- [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->