<properties
   pageTitle="Continuità aziendale cloud - Ripristino temporizzato | Microsoft Azure"
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
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="sstein"/>

# Panoramica: Ripristino temporizzato del database SQL

La funzionalità di ripristino temporizzato consente di ripristinare un database a un punto precedente nel tempo.

Il servizio di database SQL di Azure protegge tutti i database con un backup automatizzato che viene conservato per 7 giorni per l'opzione Basic, 14 giorni per l'opzione Standard e 35 giorni per l'opzione Premium. Il ripristino temporizzato è progettato per il recupero del database da questi backup dopo l'eliminazione o danneggiamento accidentale dei dati.

I backup del database vengono eseguiti automaticamente senza alcuna necessità di fornire il consenso esplicito e senza costi aggiuntivi. Le funzionalità automatiche di backup e ripristino temporizzato offrono un modo per proteggere i database da un danneggiamento o un'eliminazione accidentale a costo zero e senza l'intervento dell'amministratore, qualunque sia la causa.


|Attività (portale) | PowerShell | REST |
|:--|:--|:--|
| [Ripristinare un database SQL a un momento precedente ](sql-database-point-in-time-restore-portal.md) | [PowerShell](sql-database-point-in-time-restore-powershell.md) | [REST (createMode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |
| [Ripristinare un database SQL eliminato](sql-database-restore-deleted-database-portal.md) | [PowerShell](sql-database-restore-deleted-database-powershell.md) | [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|



## Informazioni sui backup automatici

Tutti i database Basic, Standard e Premium sono protetti da backup automatici. I backup completi vengono eseguiti ogni settimana, i backup differenziali ogni giorno e i backup del log ogni 5 minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. In genere questo viene completato entro 30 minuti, ma può richiedere più tempo. Se un database è già di grandi dimensioni, ad esempio se viene creato come risultato della copia o del ripristino di un database da un database di grandi dimensioni, il primo backup completo potrebbe richiedere più tempo. Dopo il primo backup completo l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. I tempi esatti dei backup completi e differenziali sono determinati dal sistema per bilanciare il carico complessivo. I file di backup vengono archiviati in un account di archiviazione con ridondanza geografica con accesso in lettura (RA-GRS) per assicurare la disponibilità a scopo di ripristino di emergenza.


## Tempo di ripristino per un ripristino temporizzato

Il tempo impiegato per ripristinare un database dipende da molti fattori, tra cui la dimensione del database, il punto di ripristino selezionato e la quantità di attività che deve essere ripetuta per ricostituire lo stato nel punto selezionato. Per un database molto grande e/o attivo il ripristino potrebbe richiedere diverse ore. Il ripristino di un database crea sempre un nuovo database nello stesso server del database originale, quindi al database ripristinato deve essere assegnato un nuovo nome.


Il database può essere ripristinato a qualsiasi livello di prestazioni o pool elastico. È necessario avere a disposizione una quota DTU sufficiente nel server o nel pool tenendo presente che il ripristino crea un nuovo database e che il livello di prestazioni e il livello di servizio del database ripristinato potrebbe essere diverso dallo stato corrente del database attivo. Al termine, il database ripristinato è un normale database online completamente accessibile alle tariffe normali in base al livello di prestazioni e al livello di servizio. Se si sta ripristinando il database per scopi di recupero è possibile considerare il database ripristinato come una sostituzione per il database originale o usarlo per recuperare i dati e quindi aggiornare il database originale. Se il database ripristinato è da intendersi come una sostituzione per il database originale, è necessario verificare che il livello di prestazioni e/o livello di servizio sia appropriato e ridimensionare il database se necessario. È possibile rinominare il database originale e quindi assegnare al database ripristinato il nome originale usando il comando ALTER DATABASE in T-SQL. Se si prevede di recuperare dati dal database ripristinato, sarà necessario scrivere ed eseguire separatamente qualsiasi script di ripristino dei dati sia necessario. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database sarà visibile nell'intero elenco dei database. Se si elimina il database durante il ripristino l'operazione verrà annullata e non verrà addebitata.



## Ripristino di un database eliminato di recente

Se si elimina un database Basic, Standard o Premium, il backup finale viene conservato per il normale periodo di conservazione che consente di ripristinare il database nel punto in cui è stato eliminato.

Per i database eliminati il punto di ripristino è fissato all'ora di eliminazione del database. E come prima, quando si ripristina un database eliminato, può essere ripristinato solo per il server contenente il database originale. Tenerlo presente quando si elimina un server, perché non sarà possibile ripristinare i database precedentemente dislocati in tale server una volta eliminato.

## Confronto tra backup/ripristino e copia/esportazione/importazione

Il ripristino temporizzato è l'approccio consigliato per il ripristino dei database Basic, Standard e Premium dalla perdita o dal danneggiamento accidentale dei dati. Il backup e ripristino comporta costi inferiori, perché i backup non prevedono l'applicazione di alcuna tariffa a meno che non siano eccessivi, mentre verrà addebitata una tariffa per la copia del database, necessaria per garantire un'esportazione coerente delle transazioni e per l'archiviazione del file BACPAC. Inoltre, l'intervento dell'amministratore non è necessario, perché i backup sono automatici, mentre è necessario gestire o pianificare da sé le esportazioni, e offre un miglior RPO: è possibile ripristinare uno specifico stato precedente con una granularità molto più fine (un minuto) rispetto a quella normalmente impiegata per le operazioni di copia/esportazione/importazione. Infine, offre un miglior tempo di ripristino, perché il ripristino dai backup in genere è più veloce rispetto all'importazione, che implica passaggi di creazione dello schema, disabilitazione degli indici, caricamento dei dati e infine abilitazione degli indici per ogni singola tabella. La copia e l'esportazione continuano a essere la soluzione consigliata per l'archiviazione a lungo termine oltre il periodo di conservazione.


## Riepilogo

I backup automatici e il ripristino temporizzato proteggono i database dall'eliminazione o dal danneggiamento accidentale dei dati. Questa soluzione a costo zero e senza l'intervento dell'amministratore è disponibile con tutti i database SQL. Il backup e ripristino offre un miglioramento significativo rispetto alla soluzione alternativa di copia/esportazione/importazione per le esigenze di ripristino a breve termine. È consigliabile usare il ripristino temporizzato come parte della strategia di continuità aziendale e di usare l'esportazione solo se necessario per l'archiviazione a più lungo termine o a scopo di migrazione dei dati.



## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0511_2016-->