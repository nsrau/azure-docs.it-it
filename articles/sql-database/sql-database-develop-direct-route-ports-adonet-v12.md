---
title: Porte superiori a 1433 per il database SQL | Documentazione Microsoft
description: Le connessioni client da ADO.NET al database SQL di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti.
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 3f17106a-92fd-4aa4-b6a9-1daa29421f64
ms.service: sql-database
ms.custom: develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
ms.openlocfilehash: d47ee8c794d1e231507dae6bb4aa88bf19ce6418
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Porte successive alla 1433 per ADO.NET 4.5
In questo argomento viene descritto il comportamento di connessione del database SQL di Azure per i client che utilizzano ADO.NET 4.5 o versione successiva. 

> [!IMPORTANT]
> Per informazioni sull'architettura di connettività, vedere [Architettura della connettività del database SQL di Azure](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Esterno rispetto all'interno
Per le connessioni al database SQL di Azure, è necessario prima chiedere se il programma client viene eseguito *all'esterno* o *all'interno* del limite del cloud di Azure. Nelle sottosezioni vengono illustrati due scenari comuni.

#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Esterno:* il client è in esecuzione nel computer desktop
La porta 1433 è l'unica porta da aprire nel computer desktop che ospita l'applicazione client del database SQL.

#### <a name="inside-client-runs-on-azure"></a>*All'interno:* il client è in esecuzione in Azure
Quando il client viene eseguito all'interno del limite del cloud di Azure, viene utilizzato ciò che possiamo definire un *percorso diretto* per interagire con il server del database SQL. Una volta stabilita una connessione, ulteriori interazioni tra il client e il database non coinvolgono alcun proxy middleware.

La sequenza è la seguente:

1. ADO.NET 4.5 (o versione successiva) avvia una breve interazione con il cloud di Azure e riceve un numero di porta identificato in modo dinamico.
   
   * Il numero di porta identificato in modo dinamico è compreso nell'intervallo tra 11000-11999 o 14000-14999.
2. ADO.NET quindi si connette direttamente al server del database SQL, senza alcun middleware intermedio.
3. Le query vengono inviate direttamente al database e i risultati vengono restituiti direttamente al client.

Assicurarsi che l'intervallo di porte 11000-11999 e 14000-14999 nel computer client di Azure venga reso disponibile per le interazioni del client ADO.NET 4.5 con il database SQL.

* In particolare, le porte nell'intervallo devono essere libere da eventuali altri blocchi in uscita.
* Nella macchina virtuale di Azure, il **Windows Firewall con sicurezza avanzata** controlla le impostazioni della porta.
  
  * È possibile usare l'[interfaccia utente del firewall](http://msdn.microsoft.com/library/cc646023.aspx) per aggiungere una regola per cui si specifica il protocollo**TCP** con un intervallo di porte con la sintassi **11000-11999**.

## <a name="version-clarifications"></a>Chiarimenti sulla versione
In questa sezione vengono spiegati i moniker che fanno riferimento a versioni precedenti del prodotto. Sono inoltre indicate alcune associazioni di versioni tra prodotti.

#### <a name="adonet"></a>ADO.NET
* ADO.NET 4.0 supporta il protocollo TDS 7.3, ma non 7.4.
* ADO.NET 4.5 e versioni successive supportano il protocollo TDS 7.4.

## <a name="related-links"></a>Collegamenti correlati
* ADO.NET 4.6 è stato rilasciato il 20 luglio 2015. È disponibile un annuncio di blog del team .NET [qui](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 è stato rilasciato il 15 agosto 2012. È disponibile un annuncio di blog del team .NET [qui](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  
  * È disponibile un post di blog su ADO.NET 4.5.1 [qui](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).
* [Elenco versioni del protocollo TDS](http://www.freetds.org/userguide/tdshistory.htm)
* [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* [Firewall del database SQL di Azure](sql-database-firewall-configure.md)
* [Procedura: configurare le impostazioni del firewall su Database SQL](sql-database-configure-firewall-settings.md)

