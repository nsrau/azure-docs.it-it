---
title: Ottimizzare gli inserimenti bulk nel server di Database di Azure per PostgreSQL
description: Questo articolo descrive come è possibile ottimizzare le operazioni di inserimento bulk nel server di Database di Azure per PostgreSQL.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 2fe3c3cc71823880d71223334b89816199561ca9
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628694"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Ottimizzazione degli inserimenti bulk e uso dei dati temporanei nel server di Database di Azure per PostgreSQL 
Questo articolo descrive come è possibile ottimizzare le operazioni di inserimento bulk e l'uso di dati temporanei in un server di Database di Azure per PostgreSQL.

## <a name="using-unlogged-tables"></a>Uso di tabelle non registrate
Per i clienti con operazioni di carico di lavoro che coinvolgono dati temporanei o l'inserimento bulk di grandi set di dati, prendere in considerazione l'uso di tabelle non registrate.

Le tabelle non registrate sono una funzionalità di PostgreSQL che può essere usata in modo efficace per ottimizzare gli inserimenti bulk. PostgreSQL usa WAL (Write-Ahead Logging) che offre due delle proprietà ACID per impostazione predefinita, ovvero atomicità e durabilità. Con l'inserimento in una tabella non registrata, PostgreSQL esegue gli inserimenti senza scrivere nel log delle transazioni, che a sua volta è un'operazione di I/O, rendendo così tali tabelle notevolmente più veloci rispetto alle tabelle normali.

Di seguito sono riportate le opzioni disponibili per la creazione di una tabella non registrata:
- Creare una nuova tabella registrata con la sintassi: `CREATE UNLOGGED TABLE <tableName>`
- Convertire una tabella registrata esistente in una tabella non registrata con la sintassi: `ALTER <tableName> SET UNLOGGED`.  Questa operazione può essere invertita con la sintassi: `ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>Compromessi per le tabelle non registrate
Le tabelle non registrate non sono protette dagli arresti anomali del sistema. Una tabella non registrata viene troncata automaticamente dopo un arresto anomalo o in seguito a un arresto non corretto. Il contenuto di una tabella non registrata non viene inoltre replicato nei server di standby. Gli indici creati in una tabella non registrata sono automaticamente non registrati.  Al termine dell'operazione di inserimento, si può convertire la tabella in registrata in modo che l'inserimento sia durevole.

Tuttavia, per alcuni dei carichi di lavoro dei clienti è stato registrato un miglioramento delle prestazioni del 15-20% quando si usano tabelle non registrate.

## <a name="next-steps"></a>Passaggi successivi
Esaminare il carico di lavoro per l'uso di dati temporanei e inserimenti bulk di grandi dimensioni.  

Consultare la documentazione di PostgreSQL seguente: [Create Table SQL Commands](https://www.postgresql.org/docs/current/static/sql-createtable.html) (Comandi SQL CREATE TABLE)