---
title: Supporto Self-help per SQL su richiesta (anteprima)
description: Questa sezione contiene informazioni che consentono di risolvere i problemi relativi a SQL su richiesta (anteprima).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 7a6b145e9a1efb29bbb6c233f2a09498b4a4ea7f
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213126"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Supporto Self-help per SQL su richiesta (anteprima)

Questo articolo contiene informazioni su come risolvere i problemi più frequenti di SQL su richiesta (anteprima) in Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>L'opzione SQL su richiesta è disattivata in Synapse Studio

Se Synapse Studio non riesce a stabilire una connessione con SQL su richiesta, si noterà che la relativa opzione è disattivata o che lo stato visualizzato è "Offline". Questo problema si verifica in genere in uno dei casi seguenti:

1) La rete impedisce la comunicazione con il back-end di Azure Synapse. Il caso più frequente è che la porta 1443 è bloccata. Per il corretto funzionamento di SQL su richiesta, sbloccare questa porta. Anche altri problemi possono impedire il funzionamento di SQL su richiesta. Per altre informazioni, vedere la [guida completa alla risoluzione dei problemi](../troubleshoot/troubleshoot-synapse-studio.md).
2) Non si hanno le autorizzazioni per accedere a SQL su richiesta. Per ottenere l'accesso, è necessario che si venga aggiunti al ruolo di amministratore dell'area di lavoro o amministratore di SQL da uno degli amministratori dell'area di lavoro di Azure Synapse. [Per altre informazioni, vedere la guida completa sul controllo di accesso](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>La query non riesce perché non è possibile aprire il file

Se la query non riesce con un messaggio di errore che indica che non è possibile aprire il file perché non esiste o è in uso da un altro processo e si è certi che il file esista e che non sia in uso da un altro processo, significa che SQL su richiesta non può accedere al file. Questo problema si verifica in genere perché l'identità di Azure Active Directory non ha i diritti per accedere al file. Per impostazione predefinita, SQL su richiesta prova ad accedere al file con l'identità di Azure Active Directory. Per risolvere questo problema, è necessario avere i diritti appropriati per l'accesso al file. Il modo più semplice consiste nel concedere a se stessi il ruolo 'Collaboratore ai dati dei BLOB di archiviazione' nell'account di archiviazione su cui si prova a eseguire query. [Per altre informazioni, vedere la guida completa sul controllo di accesso di Azure Active Directory per l'archiviazione](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>La query non riesce perché non può essere eseguita a causa di vincoli di risorse correnti 

Se la query non riesce con un messaggio che indica che non può essere eseguita a causa di vincoli di risorse correnti, significa che SQL su richiesta non è in grado di eseguirla al momento a causa di tali vincoli: 

- Assicurarsi di usare tipi di dati di dimensioni ragionevoli. Specificare inoltre lo schema per i file Parquet per le colonne di stringhe, che sarà VARCHAR(8000) per impostazione predefinita. 

- Se la query è destinata a file CSV, valutare se [creare statistiche](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Per ottimizzare le query, vedere [Procedure consigliate per le prestazioni per SQL su richiesta](best-practices-sql-on-demand.md).  

## <a name="create-statement-is-not-supported-in-master-database"></a>CREATE 'STATEMENT' non è supportata nel database master

Se la query ha esito negativo con il messaggio di errore:

> 'Non è stato possibile eseguire la query. Errore: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT non è supportata nel database master.' 

significa che il database master in SQL su richiesta non supporta la creazione di:
  - Tabelle esterne
  - Origini dati esterne
  - Credenziali con ambito database
  - Formati di file esterni

Soluzione:

  1. creare un database utente:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Eseguire l’istruzione di creazione nel contesto di <DATABASE_NAME> che non era riuscito in precedenza per il database master. 
  
  Esempio di creazione di un formato file esterno:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di SQL su richiesta, vedere gli articoli seguenti:

- [Eseguire query su un singolo file CSV](query-single-csv-file.md)

- [Eseguire query su cartelle e più file CSV](query-folders-multiple-csv-files.md)

- [Eseguire query su file specifici](query-specific-files.md)

- [Eseguire query su file Parquet](query-parquet-files.md)

- [Eseguire query su tipi annidati di Parquet](query-parquet-nested-types.md)

- [Eseguire query su file JSON](query-json-files.md)

- [Creazione e uso delle viste](create-use-views.md)

- [Creazione e uso di tabelle esterne](create-use-external-tables.md)

- [Archiviare i risultati delle query nell'archiviazione](create-external-table-as-select.md)
