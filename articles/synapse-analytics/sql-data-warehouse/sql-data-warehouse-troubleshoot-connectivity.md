---
title: Risoluzione dei problemi di connettività
description: Risoluzione dei problemi di connettività nel pool SQL sinapsi.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: d69c8dd28b946df3fff500c31c7cdefa4767c0c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408155"
---
# <a name="troubleshooting-connectivity-issues"></a>Risoluzione dei problemi di connettività

Questo articolo elenca le tecniche di risoluzione dei problemi comuni per la connessione al database di analisi SQL.

## <a name="check-service-availability"></a>Controllare la disponibilità del servizio

Verificare se il servizio è disponibile. Nel portale di Azure andare al pool di sinapsi SQL che si sta tentando di connettere. Nel riquadro a sinistra fare clic su **diagnostica e risoluzione dei problemi**.

![Seleziona integrità risorse](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Lo stato del pool SQL sinapsi verrà visualizzato qui. Se il servizio non viene visualizzato come **disponibile**, verificare i passaggi successivi.

![Servizio disponibile](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se l'integrità delle risorse indica che l'istanza del pool SQL sinapsi è sospesa o ridimensionata, seguire le indicazioni per riprendere l'istanza.

![Il servizio ha](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) sospeso informazioni aggiuntive su integrità risorse è disponibile qui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificare l'eventuale presenza di operazioni di scalabilità o in pausa

Controllare il portale per verificare se l'istanza del pool SQL sinapsi è sospesa o ridimensionata.

![Servizio sospeso](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se il servizio è in pausa o in scala, verificare che non sia durante la pianificazione della manutenzione. Nel portale per la *Panoramica*del pool SQL di sinapsi verrà visualizzata la pianificazione di manutenzione eletta.

![Panoramica della pianificazione di manutenzione](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

In caso contrario, rivolgersi all'amministratore IT per verificare che la manutenzione non sia un evento pianificato. Per riprendere l'istanza di SQL Analytics, attenersi alla [seguente procedura](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Verificare le impostazioni del firewall

Il database di analisi SQL comunica sulla porta 1433.Se si sta provando a connettersi da una rete aziendale, il traffico in uscita sulla porta 1433 potrebbe non essere consentito dal firewall della rete. In questo caso non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 1433. Altre informazioni sulle configurazioni del firewall sono disponibili [qui](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verificare le impostazioni dell'endpoint di servizio/rete virtuale

Se vengono ricevuti gli errori 40914 e 40615, vedere la [Descrizione e la risoluzione dell'errore](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Controllare se sono presenti i driver più recenti

### <a name="software"></a>Software

Assicurarsi di usare gli strumenti più recenti per connettersi al pool SQL sinapsi:

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Driver

Assicurarsi di usare le versioni più recenti del driver.L'utilizzo di una versione precedente dei driver potrebbe provocare comportamenti imprevisti, poiché i driver meno recenti potrebbero non supportare le nuove funzionalità.

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

Verificare se il server abbia un carico eccessivo e se sia elevato il numero delle richieste in coda. Potrebbe essere necessario scalare verticalmente il pool SQL sinapsi per altre risorse.

## <a name="common-error-messages"></a>Messaggi di errore comuni

Errori 40914 e 40615, vedere la [Descrizione e la risoluzione dell'errore](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Si verificano ancora problemi di connettività?

Creare un [ticket di supporto](sql-data-warehouse-get-started-create-support-ticket.md) in modo che il team di progettazione possa supportare l'utente.
