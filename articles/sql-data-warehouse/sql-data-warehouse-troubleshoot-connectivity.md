---
title: Risoluzione dei problemi relativi ad Azure SQL Data Warehouse | Microsoft Docs
description: Risoluzione dei problemi relativi a SQL Data Warehouse di Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 2d7f56b65db09232af4fe7e198675ea0cfd64a25
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595486"
---
# <a name="troubleshooting-connectivity-issues"></a>Risoluzione dei problemi di connettività

Questo articolo elenca le tecniche di risoluzione dei problemi più comuni per la connessione a SQL Data Warehouse.
- [Verificare la disponibilità del servizio](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Verificare la presenza di operazione di sospensione o ridimensionamento](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Controllare le impostazioni del firewall](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Controllare le impostazioni di Endpoint servizio/rete virtuale](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Cerca i driver più recenti](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Controllare la stringa di connessione](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemi di connessione intermittente](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Messaggi di errore comuni](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Verificare la disponibilità del servizio

Verificare se il servizio è disponibile. Nel portale di Azure, passare a SQL data warehouse che si sta provando a connettersi. Nel pannello del sommario a sinistra, fare clic su **diagnosticare e risolvere i problemi**.

![Selezionare l'integrità delle risorse](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Verrà visualizzato lo stato di SQL data warehouse. Se il servizio non vengono visualizzati come **Available**, controllare ulteriormente questa procedura.

![Servizio disponibile](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se l'integrità delle risorse viene mostrato che il data warehouse è sospeso o la scala, seguire le indicazioni per riprendere il data warehouse.

![Servizio sospeso](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) ulteriori informazioni su integrità risorse sono reperibile qui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificare la presenza di operazione di sospensione o ridimensionamento

Controllare il portale per vedere se viene sospesa di SQL data warehouse o il ridimensionamento.

![Servizio sospeso](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se si può osservare che il servizio è in pausa o ridimensionamento, verificare che non è durante la pianificazione di manutenzione. Nel portale per SQL data warehouse *Panoramica*, si noterà la pianificazione di manutenzione selezionati.

![Cenni preliminari sulla pianificazione della manutenzione](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

In caso contrario, rivolgersi all'amministratore IT per verificare che tali operazioni di manutenzione non è un evento pianificato. Per riprendere il SQL data warehouse, seguire i passaggi illustrati [qui](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Controllare le impostazioni del firewall

SQL Data Warehouse comunica attraverso la porta 1433.   Se si sta provando a connettersi da una rete aziendale, il traffico in uscita sulla porta 1433 potrebbe non essere consentito dal firewall della rete. In tal caso, è possibile connettersi al server di Database SQL di Azure, a meno che il reparto IT non apra la porta 1433. Altre informazioni sulle configurazioni di firewall sono reperibili [qui](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Controllare le impostazioni di Endpoint servizio/rete virtuale

Se si ricevono errori 40914 e 40615, vedere [descrizione dell'errore e la risoluzione qui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Cerca i driver più recenti

### <a name="software"></a>Software

Selezionare questa opzione per assicurarsi che si sta usando gli strumenti più recenti per connettersi a SQL data warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Driver

Verificare che sia in uso le ultime versioni di driver.  Usa una versione precedente dei driver potrebbe causare comportamenti imprevisti quando i driver precedenti potrebbero non supportare le nuove funzionalità.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Controllare la stringa di connessione

Selezionare questa opzione per assicurarsi che le stringhe di connessione siano impostate correttamente.  Di seguito sono riportati alcuni esempi.  È possibile trovare informazioni aggiuntive [stringhe di connessione qui](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Stringa di connessione ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Stringa di connessione ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Stringa di connessione PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Stringa di connessione JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemi di connessione intermittente

Verificare se sono stati riscontrati elevato carico sul server con un numero elevato di richieste in coda. Potrebbe essere necessario aumentare le prestazioni del data warehouse per risorse aggiuntive.

## <a name="common-error-messages"></a>Messaggi di errore comuni

Errori 40914 e 40615, vedere la [descrizione dell'errore e la risoluzione qui](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>I problemi di connettività persistono,
Creare un [ticket di supporto](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) dunque può supportare il team di progettazione è.
