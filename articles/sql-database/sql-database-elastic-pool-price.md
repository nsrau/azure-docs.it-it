<properties
	pageTitle="Prestazioni e prezzi dei pool di database elastici del database SQL"
	description="Informazioni specifiche sui prezzi dei pool di database elastici"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/11/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Informazioni su prezzi e fatturazione dei pool di database elastici

I pool di database elastici vengono fatturati in base alle caratteristiche seguenti:

- Un pool elastico viene fatturato al momento della creazione, persino quando nel pool non sono presenti database.
- Un pool elastico viene fatturato su base oraria. Si tratta della stessa frequenza di controllo dei livelli di prestazioni dei database singoli.
- Se un pool elastico viene ridimensionato per una nuova quantità di eDTU, non viene fatturato in base alla nuova quantità di eDTU fino al completamento dell'operazione di ridimensionamento. Tale comportamento segue lo stesso modello della modifica del livello di prestazioni dei database autonomi.


- Il prezzo di un pool elastico è basato sul numero di eDTU del pool. Il prezzo di un pool elastico è indipendente dal numero e dall'uso dei database elastici in esso contenuti.
- Il prezzo di base viene calcolato da (numero di pool eDTUs) x (prezzo unitario per eDTU).

Il prezzo unitario delle eDTU per un pool elastico è superiore al prezzo unitario delle eDTU per un database autonomo nello stesso livello di servizio. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/).


Per comprendere i livelli di eDTU e servizio, vedere l'articolo relativo a [opzioni e prestazioni del database SQL](sql-database-service-tiers.md).

## Passaggi successivi

- [Creare un pool di database elastici](sql-database-elastic-pool-create-portal.md)
- [Monitor, manage, and size an elastic database pool (Monitorare, gestire e ridimensionare un pool di database elastici)](sql-database-elastic-pool-manage-portal.md)
- [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md)
- [Script di PowerShell per identificare database adatti a un pool di database elastici](sql-database-elastic-pool-database-assessment-powershell.md)

<!---HONumber=AcomDC_0413_2016-->