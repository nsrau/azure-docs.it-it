---
title: "Guida introduttiva: configurare l'isolamento del carico di lavoro - T-SQL"
description: Usare T-SQL per configurare l'isolamento del carico di lavoro.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 10/29/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 92f8aaad1cc3279142d419faa2852406c2956595
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685973"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Guida introduttiva: configurare l'isolamento del carico di lavoro tramite T-SQL

In questa Guida introduttiva verranno creati rapidamente un gruppo di carico di lavoro e un classificatore per prenotare risorse per il caricamento dei dati. Il gruppo di carico di lavoro allocherà il 20% delle risorse di sistema a un carico di dati.  Il classificatore del carico di lavoro assegnerà le richieste al gruppo di carico di lavoro per i dati.  Con un isolamento del 20% per il caricamento dei dati, si tratta di risorse garantite per soddisfare i contratti di servizio.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile.  Per altre informazioni, vedere [SQL Data Warehouse Prezzi](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Prerequisiti
 
Questa guida di avvio rapido presuppone che l'utente abbia già un'istanza di SQL Data Warehouse con autorizzazioni CONTROL DATABASE. Se è necessario crearne uno, fare riferimento a [Creare e connettere - portale](create-data-warehouse-portal.md) per creare un data warehouse denominato **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Creare un account di accesso per i DataLoads

Creare un account di accesso con autenticazione di SQL Server nel database `master` con l’istruzione [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) per "ELTLogin".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Create user

[Create user](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest) ed "ELTLogin" in mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Creare un gruppo di carico di lavoro
Creare un [gruppo di carico di lavoro](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) per DataLoads con il 20% di isolamento.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Creare un classificatore del carico di lavoro

Creare un [classificatore del carico di lavoro](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) per eseguire il mapping di ELTLogin al gruppo di carico di lavoro DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'ELTLogin'
      ,MEMBERNAME = 'DataLoads')
;
```

## <a name="view-existing-workload-groups-and-classifiers"></a>Visualizzare i gruppi di carico di lavoro e i classificatori esistenti

```sql
SELECT * FROM 
sys.workload_management_workload_groups

SELECT * FROM 
sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Pulire le risorse

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Per le unità del data warehouse e i dati archiviati vengono addebitati costi. Le risorse di calcolo e archiviazione vengono fatturate separatamente.

- Se si vogliono mantenere i dati nelle risorse di archiviazione, è possibile sospendere il calcolo quando il data warehouse non è in uso. In questo modo, vengono addebitati solo i costi per l'archiviazione dei dati. Quando si è pronti a lavorare con i dati, riprendere il calcolo.
- Per evitare di ricevere addebiti in futuro, è possibile eliminare il data warehouse.

Seguire questa procedura per pulire le risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il proprio data warehouse.

    ![Pulire le risorse](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Per sospendere il calcolo, selezionare il pulsante **Pausa**. Quando si sospende il data warehouse, viene visualizzato il pulsante **Avvia**.  Per riprendere il calcolo, selezionare **Avvia**.

3. Per rimuovere il data warehouse in modo da non ricevere addebiti per operazioni di calcolo o archiviazione, selezionare **Elimina**.

4. Per rimuovere il server SQL creato, selezionare **mynewserver-20180430.database.windows.net** nell'immagine precedente e quindi selezionare **Elimina**.  Fare attenzione quando si esegue questa operazione perché l'eliminazione del server comporta anche quella di tutti i database assegnati al server.

5. Per rimuovere il gruppo di risorse, selezionare **myResourceGroup** e quindi **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

- A questo punto è stato creato un gruppo di carico di lavoro. Eseguire alcune query come ELTLogin per verificarne le prestazioni. Vedere [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) per visualizzare le query e il gruppo di carico di lavoro assegnato.
- Per altre informazioni sulla gestione dei carichi di lavoro di Azure SQL Data Warehouse, vedere [Gestione dei carichi di lavoro](sql-data-warehouse-workload-management.md) e [Isolamento dei carichi di lavoro](sql-data-warehouse-workload-isolation.md).
