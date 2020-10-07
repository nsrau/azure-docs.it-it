---
title: 'Avvio rapido: Caricamento bulk di dati con una singola istruzione T-SQL'
description: Caricamento bulk di dati con l'istruzione COPY
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 06/18/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f82bedc6ef638714b2641003e8274c2024a86c2e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "85213007"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Avvio rapido: Caricamento bulk di dati con l'istruzione COPY

In questa guida di avvio rapido si eseguirà il caricamento bulk di dati nel pool SQL usando la semplice e flessibile [istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) per un inserimento dei dati a velocità effettiva elevata. L'istruzione COPY è l'utilità di caricamento consigliata, in quanto consente di caricare i dati in modo semplice e flessibile offrendo funzionalità per:

- Consentire a utenti con privilegi inferiori di caricare senza richiedere autorizzazioni CONTROL rigorose per il data warehouse
- Usare una sola istruzione T-SQL senza dover creare oggetti di database aggiuntivi
- Sfruttare un modello di autorizzazione più preciso senza esporre le chiavi dell'account di archiviazione usando le firme di accesso condiviso (SAS)
- Specificare un account di archiviazione diverso per il percorso di ERRORFILE (REJECTED_ROW_LOCATION)
- Personalizzare i valori predefiniti per ogni colonna di destinazione e specificare i campi dei dati di origine da caricare in colonne di destinazione specifiche
- Specificare un carattere di terminazione riga personalizzato per i file CSV
- Aggiungere un carattere di escape ai delimitatori di stringa, campo e riga per i file CSV
- Usare i formati di data di SQL Server per i file CSV
- Specificare caratteri jolly e più file nel percorso della posizione di archiviazione

## <a name="prerequisites"></a>Prerequisiti

Questa guida di avvio rapido presuppone che l'utente abbia già un pool SQL. Se non è stato creato un pool SQL, usare la guida di avvio rapido [Creare e connettere - Portale](create-data-warehouse-portal.md).

## <a name="set-up-the-required-permissions"></a>Configurare le autorizzazioni necessarie

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>Creare la tabella di destinazione

In questo esempio verranno caricati i dati dal set di dati New York taxi. Verrà caricata una tabella denominata Trip che rappresenta le corse in taxi effettuate in un dato anno. Eseguire l'istruzione seguente per creare la tabella:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>Eseguire l'istruzione COPY

Eseguire l'istruzione COPY seguente che caricherà i dati dell'account di archiviazione BLOB di Azure nella tabella Trip.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>Monitorare il caricamento

Per controllare lo stato di avanzamento del caricamento, eseguire periodicamente la query seguente:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>Passaggi successivi

- Per le procedure consigliate relative al caricamento dei dati, vedere [Procedure consigliate per il caricamento dei dati](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Per informazioni su come gestire le risorse per i caricamenti dei dati, vedere [Isolamento del carico di lavoro](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
