---
title: Porte successive alla 1433
description: Le connessioni client da ADO.NET al database SQL di Azure possono ignorare il proxy e interagire direttamente con il database usando porte diverse da 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, devx-track-dotnet
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 06/11/2020
ms.openlocfilehash: 0d009522ea0d0986233983f8725549b618ffb537
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444867"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porte successive alla 1433 per ADO.NET 4.5
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In questo argomento viene descritto il comportamento di connessione del database SQL di Azure per i client che utilizzano ADO.NET 4.5 o versione successiva.

> [!IMPORTANT]
> Per informazioni sull'architettura di connettività, vedere [Architettura della connettività del database SQL di Azure](connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Esterno rispetto all'interno

Per le connessioni al database SQL di Azure, è necessario prima chiedere se il programma client viene eseguito *all'esterno* o *all'interno* del limite del cloud di Azure. Nelle sottosezioni vengono illustrati due scenari comuni.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Esterno:* il client è in esecuzione nel computer desktop

La porta 1433 è l'unica porta da aprire nel computer desktop che ospita l'applicazione client del database SQL.

### <a name="inside-client-runs-on-azure"></a>*All'interno:* il client è in esecuzione in Azure

Quando il client viene eseguito all'interno del limite del cloud di Azure, USA ciò che è possibile chiamare una *route diretta* per interagire con il database SQL. Una volta stabilita una connessione, altre interazioni tra il client e il database non coinvolgono alcun gateway di database SQL di Azure.

La sequenza è la seguente:

1. ADO.NET 4.5 (o versione successiva) avvia una breve interazione con il cloud di Azure e riceve un numero di porta identificato in modo dinamico.

   * Il numero di porta identificato in modo dinamico è compreso nell'intervallo 11000-11999.
2. ADO.NET si connette quindi direttamente al database SQL, senza alcun middleware.
3. Le query vengono inviate direttamente al database e i risultati vengono restituiti direttamente al client.

Assicurarsi che gli intervalli di porte di 11000-11999 nel computer client di Azure siano disponibili per le interazioni del client ADO.NET 4,5 con il database SQL.

* In particolare, le porte nell'intervallo devono essere libere da eventuali altri blocchi in uscita.
* Nella macchina virtuale di Azure, il **Windows Firewall con sicurezza avanzata** controlla le impostazioni della porta.
  
  * È possibile usare l'[interfaccia utente del firewall](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access) per aggiungere una regola per cui si specifica il protocollo**TCP** con un intervallo di porte con la sintassi **11000-11999**.

## <a name="version-clarifications"></a>Chiarimenti sulla versione

In questa sezione vengono spiegati i moniker che fanno riferimento a versioni precedenti del prodotto. Sono inoltre indicate alcune associazioni di versioni tra prodotti.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 supporta il protocollo TDS 7.3, ma non 7.4.
* ADO.NET 4.5 e versioni successive supportano il protocollo TDS 7.4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 o versione successiva

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 o versione successiva. JDBC 4.0 supporta TDS 7.4 ma non implementa il reindirizzamento

## <a name="related-links"></a>Collegamenti correlati

* ADO.NET 4.6 è stato rilasciato il 20 luglio 2015. È disponibile un annuncio di blog del team .NET [qui](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-6/).
* ADO.NET 4.5 è stato rilasciato il 15 agosto 2012. È disponibile un annuncio di blog del team .NET [qui](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code/).
  * È disponibile un post di blog su ADO.NET 4.5.1 [qui](https://devblogs.microsoft.com/dotnet/announcing-the-net-framework-4-5-1-preview/).

* Microsoft ODBC driver 17 for SQL Server https://aka.ms/downloadmsodbcsql

* Connect to Azure SQL Database V12 via Redirection https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362 (Connettersi al database SQL di Azure versione 12 tramite reindirizzamento)

* [Elenco versioni del protocollo TDS](https://www.freetds.org/)
* [Panoramica dello sviluppo di database SQL](develop-overview.md)
* [Firewall del database SQL di Azure](firewall-configure.md)
