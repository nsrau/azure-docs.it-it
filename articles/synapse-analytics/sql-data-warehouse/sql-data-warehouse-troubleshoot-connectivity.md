---
title: Risoluzione dei problemi di connettività
description: Risoluzione dei problemi di connettività nel pool Synapse SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: d32a51e391edbfd32cf57265562d4e0cb8fe0681
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362181"
---
# <a name="troubleshooting-connectivity-issues-in-synapse-sql-pool"></a>Risoluzione dei problemi di connettività nel pool Synapse SQL

Questo articolo illustra le comuni tecniche di risoluzione dei problemi riguardanti la connessione ai database del pool SQL.

## <a name="check-service-availability"></a>Verificare la disponibilità del servizio

Verificare se il servizio è disponibile. Nel portale di Azure passare al pool SQL a cui si sta provando a connettersi. Nel pannello del sommario fare clic su **Diagnostica e risoluzione dei problemi**.

![Selezionare Integrità risorsa](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Verrà visualizzato lo stato del pool SQL. Se il servizio non viene indicato come **Disponibile**, procedere ad altre verifiche.

![Servizio disponibile](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se Integrità risorsa indica che l'istanza del pool SQL è in pausa o in fase di ridimensionamento, seguire le istruzioni per riprenderne l'esecuzione.

![Screenshot mostra un'istanza di SQL data warehouse sospesa o in scala.](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Altre informazioni su Integrità risorse sono disponibili qui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificare la presenza di operazioni in pausa o in fase di ridimensionamento

Controllare il portale per verificare se l'istanza del pool SQL è in pausa o in fase di ridimensionamento.

![Screenshot che illustra come verificare se un data warehouse è sospeso.](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se il servizio risulta in pausa o in fase di ridimensionamento, verificare se è in corso un intervento di manutenzione pianificata. Nella finestra *Panoramica* del portale per il pool SQL verrà visualizzata la data pianificata per la manutenzione.

![Pianificazione della manutenzione nella panoramica](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

In caso contrario, rivolgersi all'amministratore IT per verificare se questo evento di manutenzione non è pianificato. Per riprendere l'esecuzione dell'istanza del pool SQL, seguire [questa procedura](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Verificare le impostazioni del firewall

Il database del pool SQL comunica sulla porta 1433.  Se si sta provando a connettersi da una rete aziendale, il traffico in uscita sulla porta 1433 potrebbe non essere consentito dal firewall della rete. In tal caso, non è possibile connettersi al [server logico](../../azure-sql/database/logical-servers.md) , a meno che il reparto IT non apra la porta 1433. Per altre informazioni sulle configurazioni del firewall, vedere [qui](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verificare le impostazioni dell'endpoint di servizio/rete virtuale

Se si ricevono gli errori 40914 e 40615, vedere le [descrizioni e risoluzioni degli errori](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Controllare se sono presenti i driver più recenti

### <a name="software"></a>Software

Assicurarsi di usare gli strumenti più recenti per connettersi al pool SQL:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Driver

Assicurarsi di usare le versioni più recenti dei driver.  L'uso di una versione meno recente dei driver potrebbe generare comportamenti imprevisti, perché questi driver potrebbero non supportare le nuove funzionalità.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Verificare la stringa di connessione

Accertarsi che le stringhe di connessione siano impostate in modo appropriato.  Di seguito sono riportati alcuni esempi.  È possibile trovare informazioni aggiuntive sulle [stringhe di connessione qui](sql-data-warehouse-connection-strings.md).

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

## <a name="intermittent-connection-issues"></a>Problemi di connessione intermittenti

Verificare se il server abbia un carico eccessivo e se sia elevato il numero delle richieste in coda. Potrebbe essere necessario aumentare le risorse del pool SQL.

## <a name="common-error-messages"></a>Messaggi di errore comuni

Per informazioni sugli errori 40914 e 40615, vedere le [descrizioni e risoluzioni degli errori](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>I problemi di connettività persistono?

Creare un [ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) per chiedere assistenza al team di tecnici.
