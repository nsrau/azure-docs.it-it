---
title: Disabilitare Estensione database e recuperare i dai remoti | Documentazione Microsoft
description: Scoprire come disabilitare Database Estensione per una tabella e scegliere di recuperare i dati remoti.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 08291748-0dfd-4a7d-a6a4-a5618e9c248d
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a140e89058f6a246e80b71248b82d0f6767337cb


---
# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Disabilitare Database Estensione e recuperare i dati remoti
Per disabilitare Database Estensione per una tabella, selezionare **Estensione** per una tabella in SQL Server Management Studio. Selezionare quindi una delle opzioni seguenti.

* **Disabilita | Ripristina dati da Azure**. Copiare i dati remoti per la tabella da Azure a SQL Server, quindi disabilitare Database Estensione per la tabella. Questa operazione comporta costi di trasferimento dati e non può essere annullata.
* **Disabilita | Lascia dati in Azure**. Disabilitare Database Estensione per la tabella.  Abbandonare i dati remoti per la tabella in Azure.

È anche possibile usare Transact\-SQL per disabilitare Estensione database per una tabella o per un database.

Dopo aver disabilitato Database Estensione per una tabella, la migrazione di dati viene interrotta e i risultati della query non includono più i risultati della tabella remota.

Se si vuole semplicemente sospendere la migrazione dei dati, vedere [Sospendere e riprendere la migrazione dei dati (Estensione database)](sql-server-stretch-database-pause.md).

> [!NOTE]
> La disabilitazione del database di estensione per una tabella o per un database non elimina l'oggetto remoto. Per eliminare la tabella o il database remoto è necessario rimuoverli usando il portale di gestione di Azure. Gli oggetti remoti continuano a comportare l'addebito di costi in Azure fino a quando non vengono eliminati. Per altre informazioni, vedere i [prezzi di Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

## <a name="disable-stretch-database-for-a-table"></a>Disabilitare Database Estensione per una tabella
### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Con SQL Server Management Studio disabilitare Database Estensione per una tabella
1. In SQL Server Management Studio, in Esplora oggetti, selezionare la tabella per cui si desidera disabilitare Database Estensione.
2. Fare clic con il pulsante destro del mouse e scegliere **Estensione**, quindi selezionare una delle opzioni seguenti.
   
   * **Disabilita | Ripristina dati da Azure**. Copiare i dati remoti per la tabella da Azure a SQL Server, quindi disabilitare Database Estensione per la tabella. Questo comando non può essere annullato.
     
     > [!NOTE]
     > La copia dei dati remoti per la tabella da Azure a SQL Server comporta costi di trasferimento dati. Per altre informazioni, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).
     > 
     > 
     
     Dopo aver copiato tutti i dati remoti da Azure a SQL Server, l'estensione per la tabella risulta disabilitata.
   * **Disabilita | Lascia dati in Azure**. Disabilitare Database Estensione per la tabella.  Abbandonare i dati remoti per la tabella in Azure.
   
   > [!NOTE]
   > La disabilitazione del database di estensione per una tabella non elimina i dati remoti o la tabella remota. Per eliminare la tabella remota è necessario rimuoverla usando il portale di gestione di Azure. La tabella remota continua a comportare costi di Azure fino alla sua eliminazione. Per altre informazioni, vedere i [prezzi di Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
   > 
   > 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Usare Transact\-SQL per disabilitare Estensione database per una tabella
* Per disabilitare l'estensione per una tabella e copiare i dati remoti per la tabella da Azure a SQL Server, eseguire il comando seguente. Dopo aver copiato tutti i dati remoti da Azure a SQL Server, l'estensione per la tabella risulta disabilitata.
  
  Questo comando non può essere annullato.
  
  ```tsql
  USE <Stretch-enabled database name>;
  GO
  ALTER TABLE <Stretch-enabled table name>  
     SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
  GO
  ```
  > [!NOTE]
  > La copia dei dati remoti per la tabella da Azure a SQL Server comporta costi di trasferimento dati. Per altre informazioni, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).
  > 
  > 
* Per disabilitare l'estensione per una tabella e abbandonare i dati remoti, eseguire il comando seguente.
  
  ```tsql
  ALTER TABLE <table_name>
     SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
  ```

> [!NOTE]
> La disabilitazione del database di estensione per una tabella non elimina i dati remoti o la tabella remota. Per eliminare la tabella remota è necessario rimuoverla usando il portale di gestione di Azure. La tabella remota continua a comportare costi di Azure fino alla sua eliminazione. Per altre informazioni, vedere i [prezzi di Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

## <a name="disable-stretch-database-for-a-database"></a>Disabilitare Database Estensione per un database
Prima di disabilitare Estensione database per un database, è necessario disabilitare Estensione database su singole tabelle abilitate per l'estensione nel database.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Con SQL Server Management Studio disabilitare Database Estensione per un database
1. In SQL Server Management Studio, in Esplora oggetti, selezionare il database per cui si desidera disabilitare Database Estensione.
2. Fare clic con il pulsante destro del mouse e scegliere **Attività**, quindi **Estensione** e **Disabilita**.

> [!NOTE]
> La disabilitazione del database di estensione per un database non elimina il database remoto. Per eliminare il database remoto è necessario rimuoverlo usando il portale di gestione di Azure. Il database remoto continua a comportare costi di Azure fino alla sua eliminazione. Per altre informazioni, vedere i [prezzi di Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Usare Transact\-SQL per disabilitare Estensione database per un database
Eseguire il comando indicato di seguito.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

> [!NOTE]
> La disabilitazione del database di estensione per un database non elimina il database remoto. Per eliminare il database remoto è necessario rimuoverlo usando il portale di gestione di Azure. Il database remoto continua a comportare costi di Azure fino alla sua eliminazione. Per altre informazioni, vedere i [prezzi di Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

## <a name="see-also"></a>Vedere anche
[Opzioni ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md)




<!--HONumber=Nov16_HO3-->


