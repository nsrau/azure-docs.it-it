<properties
   pageTitle="Continuità aziendale cloud - Ripristino geografico | Microsoft Azure"
   description="Informazioni su come il database SQL di Azure supporta la continuità aziendale cloud e il ripristino del database e consente di mantenere le applicazioni cloud cruciali in esecuzione."
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

# Panoramica: ripristino geografico di un database SQL di Azure

> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-restore.md)
- [Portale di Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Il ripristino geografico consente di ripristinare un database SQL dal backup giornaliero più recente ed è abilitato automaticamente per tutti i livelli di servizio senza costi aggiuntivi. Il ripristino geografico usa un backup con ridondanza geografica come origine e può essere usato per ripristinare un database anche se il database o il data center è inaccessibile a causa di un guasto.

All'avvio del ripristino geografico viene creato un nuovo database SQL che può essere creato in qualsiasi server in qualsiasi area di Azure.

> [AZURE.NOTE] È anche possibile usare [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


Il ripristino geografico è l'opzione di ripristino predefinita quando il database non è disponibile a causa di un evento imprevisto nell'area in cui è ospitato. Il database può essere creato su qualunque server in qualsiasi area di Azure. Il ripristino geografico si basa su [backup automatici del database](sql-database-automated-backups.md) nella risorsa di archiviazione di Azure con ridondanza geografica ed esegue il ripristino dalla copia di backup con replica geografica, risultando quindi resiliente alle interruzioni dell'archiviazione nell'area primaria.



## Informazioni dettagliate sul ripristino geografico

Il ripristino geografico usa la stessa tecnologia del ripristino temporizzato, con una differenza importante. Ripristina il database da una copia del backup giornaliero più recente nell'archivio BLOB con replica geografica (RA-GRS). Per ogni database attivo, il servizio mantiene una catena di backup che include un backup completo settimanale, più backup giornalieri differenziali e log delle transazioni salvati ogni 5 minuti. Questi BLOB con replica geografica garantiscono la disponibilità dei backup giornalieri anche in seguito a un errore di notevole entità nell'area primaria. La figura seguente mostra la replica geografica di backup settimanali e giornalieri copiati nei contenitori di archiviazione.

![ripristino geografico](./media/sql-database-geo-restore/geo-restore-1.png)


Se si verifica un evento imprevisto su larga scala che determina la mancata disponibilità dell'applicazione di database, è possibile usare il ripristino geografico per ripristinare un database dal backup più recente in un server in un'altra area. Tutti i backup sono con replica geografica ed è possibile si verifichi un ritardo che tra l'acquisizione del backup e la replica geografica nel BLOB di Azure in un'area diversa. Questo ritardo può essere al massimo di un'ora, quindi in caso di emergenza può verificarsi una perdita massima di 1 ora di dati, ovvero un valore RPO fino a 1 ora. Di seguito è mostrato il ripristino del database dall'ultimo backup giornaliero.


![ripristino geografico](./media/sql-database-geo-restore/geo-restore-2.png)



## Tempo di recupero per un ripristino geografico

Il tempo di recupero è influenzato da diversi fattori: le dimensioni del database, il livello di prestazioni del database e il numero di richieste di ripristino simultanee elaborate nell'area di destinazione. Nel caso di un'interruzione prolungata in un'area, è possibile che in altre aree vengano elaborate molte richieste di ripristino geografico. In presenza di un numero elevato di richieste, i tempi di ripristino dei database in tale area potrebbero aumentare.


## Riepilogo

Il ripristino geografico, disponibile con tutti i livelli di servizio, è la più semplice delle soluzioni di ripristino di emergenza disponibili nel database SQL con il massimo valore per RPO e tempo di recupero stimato. Per i database di base con una dimensione massima di 2 GB, il ripristino geografico rappresenta una soluzione ragionevole per il ripristino di emergenza con un tempo di recupero stimato di 12 ore. Per i database Standard o Premium di dimensioni maggiori, se è auspicabile una riduzione significativa dei tempi di ripristino o per limitare le probabilità di perdita di dati, è consigliabile usare la replica geografica attiva. La replica geografica attiva offre un obiettivo del punto di ripristino e un tempo di recupero stimato decisamente inferiori perché richiede solo l'avvio di un failover in un database secondario con replica continua. Per informazioni dettagliate, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md).

## Passaggi successivi

- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Eseguire il ripristino geografico di un database SQL di Azure da un backup con ridondanza geografica tramite il portale di Azure](sql-database-geo-restore-portal.md)
- [Ripristinare un database SQL di Azure da un backup con ridondanza geografica tramite PowerShell](sql-database-geo-restore-powershell.md)

## Risorse aggiuntive

- [Domande frequenti su continuità aziendale e ripristino di emergenza nel database SQL](sql-database-bcdr-faq.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->