<properties
   pageTitle="Eseguire il ripristino di un database in Azure SQL Data Warehouse (Panoramica) | Microsoft Azure"
   description="Panoramica delle opzioni di ripristino del database per ripristinare un database in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Eseguire il ripristino di un database in Azure SQL Data Warehouse (Panoramica)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-manage-database-restore.md)
- [Portale](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Questo argomento descrive le opzioni per il ripristino di un database in Azure SQL Data Warehouse. Le attività descritte includono il ripristino di un database attivo, eliminato e inaccessibile. l database attivi ed eliminati vengono ripristinati con uno snapshot dallo stesso data center. Il database inaccessibile potrebbe essere offline a causa di un'interruzione del servizio. In questo caso, il ripristino avviene da un data center situato in una posizione geografica diversa.


## Scenari di ripristino

**Ripristino da errori dell'infrastruttura:** questo scenario si riferisce al ripristino da problemi dell'infrastruttura, ad esempio errori del disco e così via. Un cliente vuole garantire la continuità aziendale con un'infrastruttura con tolleranza di errore e disponibilità elevata.

**Ripristino da errori dell'utente:** questo scenario si riferisce al ripristino da danneggiamento o eliminazione di dati non intenzionale o accidentale. Nel caso in cui un utente modifichi o elimini dati inavvertitamente o accidentalmente, è possibile che un cliente voglia garantire la continuità aziendale ripristinando il database da un punto precedente nel tempo.

**Ripristino di emergenza:** questo scenario si riferisce al ripristino da un evento catastrofico importante. Nello scenario in cui un evento di arresto improvviso, ad esempio una calamità naturale o un'interruzione del servizio a livello locale, rende il database non disponibile, è possibile che un cliente voglia ripristinare il database in un'area diversa per continuare le operazioni aziendali.

## Criteri dello snapshot

[AZURE.INCLUDE [Criteri di conservazione dei backup di SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Funzionalità di ripristino del database

Si osservi come SQL Data Warehouse migliora l'affidabilità del database e consente la recuperabilità e il funzionamento continuo negli scenari descritti in precedenza.


### Ridondanza dei dati

SQL Data Warehouse archivia tutti i dati in Archiviazione di Azure con ridondanza geografica (RA-GRS). Con l'archiviazione con ridondanza geografica i dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria. Sia nelle aree primarie che in quelle secondarie i dati vengono replicati tre volte tra domini di errore e domini di aggiornamento separati. In questo modo viene garantita la durabilità dei dati anche in caso di una completa interruzione del servizio a livello locale o di una calamità che rende una delle aree non disponibile. Per altre informazioni sull'archiviazione con accesso in lettura e ridondanza geografica, vedere le [opzioni di ridondanza dell'archiviazione di Azure][].

### Ripristino del database

La funzionalità di ripristino del database è stata progettata per ripristinare il database da un punto precedente nel tempo. Il servizio Azure SQL Data Warehouse protegge tutti i database eseguendo snapshot di archiviazione automatica almeno ogni 8 ore e li conserva per 7 giorni in modo da fornire un set discreto di punti di ripristino. Questi snapshot vengono archiviati in Archiviazione di Azure RA-GRS e pertanto offrono ridondanza geografica per impostazione predefinita. Le funzionalità di snapshot e ripristino automatici consentono di proteggere i database da un danneggiamento o un'eliminazione accidentale a costo zero. Per altre informazioni sul ripristino di database, vedere l'articolo [Database restore tasks][] (Attività di ripristino del database).

### Ripristino geografico

Il ripristino geografico è progettato per ripristinare il database se diventa non disponibile a causa di un evento di arresto improvviso. Poiché il backup è con ridondanza geografica, è possibile usarlo per ripristinare un database anche se il database è inaccessibile a causa di un'interruzione del servizio. Il database ripristinato può essere creato su qualunque server in qualsiasi area di Azure. Oltre al ripristino dopo un'interruzione, il ripristino geografico può anche essere usato per altri scenari come ad esempio la migrazione o la copia del database in un server o un'area differenti.

**Quando avviare il ripristino** L'operazione di ripristino richiede la modifica della stringa di connessione SQL al momento del ripristino e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Usare i punti dati seguenti per determinare se il ripristino è possibile:

- Errore di connettività permanente per il database.
- Il portale di Azure visualizza un avviso relativo a un evento imprevisto con un vasto impatto nell'area.


## Passaggi successivi
Per altre importanti attività di gestione, vedere [Panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[opzioni di ridondanza dell'archiviazione di Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[Panoramica della gestione]: sql-data-warehouse-overview-management.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->