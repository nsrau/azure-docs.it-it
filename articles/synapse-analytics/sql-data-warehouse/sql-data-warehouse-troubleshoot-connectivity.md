---
title: Risoluzione dei problemi di connettività
description: Risoluzione dei problemi di connettività in Analisi SQL.
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
ms.openlocfilehash: 689a2e549c2627c607b6549f164e55a73318f63e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350052"
---
# <a name="troubleshooting-connectivity-issues"></a>Risoluzione dei problemi di connettività

Questo articolo elenca le tecniche di risoluzione dei problemi comuni relative alla connessione al database di SQL Analytics.This article lists common troubleshooting techniques around connecting to your SQL Analytics database.
- [Verificare la disponibilità del servizio](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Verificare l'eventuale presenza di operazioni di scalabilità o in pausa](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Verificare le impostazioni del firewall](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Verificare le impostazioni dell'endpoint di servizio/rete virtuale](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Controllare se sono presenti i driver più recenti](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Verificare la stringa di connessione](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemi di connessione intermittenti](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Messaggi di errore comuni](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Verificare la disponibilità del servizio

Verificare se il servizio è disponibile. Nel portale di Azure passare al database di SQL Analytics a cui si sta tentando di connettersi. Nel pannello toC sinistro, fare clic su **Diagnostica e risolvere i problemi**.

![Selezionare Integrità risorse](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Lo stato di Analisi SQL verrà visualizzato qui. Se il servizio non viene visualizzato come **Disponibile**, verificare ulteriori passaggi.

![Servizio disponibile](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Se l'integrità della risorsa indica che l'istanza di SQL Analytics è sospesa o ridimensionata, segui le indicazioni per riprendere l'istanza.

![Servizio sospeso](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Ulteriori informazioni sull'integrità delle risorse sono disponibili qui.

## <a name="check-for-paused-or-scaling-operation"></a>Verificare l'eventuale presenza di operazioni di scalabilità o in pausa

Controlla il portale per verificare se l'istanza di SQL Analytics è sospesa o ridimensionata.

![Servizio sospeso](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Se il servizio è in pausa o il ridimensionamento, verificare che non sia durante la pianificazione della manutenzione. Nel portale per la *panoramica*di SQL Analytics verrà visualizzata la pianificazione della manutenzione eletta.

![Panoramica Programma di manutenzione](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

In caso contrario, rivolgersi all'amministratore IT per verificare che questa manutenzione non sia un evento pianificato. Per riprendere l'istanza di SQL Analytics, seguire i passaggi descritti [di seguito](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Verificare le impostazioni del firewall

Il database di SQL Analytics comunica tramite la porta 1433.Se si sta provando a connettersi da una rete aziendale, il traffico in uscita sulla porta 1433 potrebbe non essere consentito dal firewall della rete. In questo caso non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 1433. Ulteriori informazioni sulle configurazioni del firewall sono disponibili [qui](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Verificare le impostazioni dell'endpoint di servizio/rete virtuale

Se si ricevono errori 40914 e 40615, vedere la descrizione e la [risoluzione](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)degli errori qui .

## <a name="check-for-the-latest-drivers"></a>Controllare se sono presenti i driver più recenti

### <a name="software"></a>Software

Verifica di utilizzare gli strumenti più recenti per connetterti al database di SQL Analytics:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Driver

Verifica di utilizzare le versioni più recenti dei driver.L'utilizzo di una versione precedente dei driver potrebbe causare comportamenti imprevisti poiché i driver meno recenti potrebbero non supportare nuove funzionalità.

* [Odbc](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [Php](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Verificare la stringa di connessione

Accertarsi che le stringhe di connessione siano impostate in modo appropriato.  Di seguito sono riportati alcuni esempi.  È possibile trovare informazioni aggiuntive sulle [stringhe di connessione qui](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

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

Verificare se il server abbia un carico eccessivo e se sia elevato il numero delle richieste in coda. Potrebbe essere necessario aumentare la scalabilità dell'istanza di SQL Analytics per altre risorse.

## <a name="common-error-messages"></a>Messaggi di errore comuni

Errori 40914 e 40615, vedere la descrizione e la [risoluzione](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)degli errori qui .

## <a name="still-having-connectivity-issues"></a>Hai ancora problemi di connettività?
Crea un ticket di [supporto](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) in modo che il team di progettazione possa supportarti.
