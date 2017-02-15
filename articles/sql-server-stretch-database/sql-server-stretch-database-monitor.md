---
title: Monitoraggio e risoluzione dei problemi di migrazione dei dati (Estensione database) | Documentazione Microsoft
description: Informazioni su come monitorare lo stato della migrazione dei dati.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 2341d446-9909-4694-8bb8-d288582daf54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4efcb1b30f002d7baecaa46f9994dfbc9ad42dee


---
# <a name="monitor-and-troubleshoot-data-migration-stretch-database"></a>Monitoraggio e risoluzione dei problemi di migrazione dei dati (Database Estensione)
Per monitorare la migrazione dei dati in Monitoraggio Database Estensione selezionare **Attività | Estensione | Monitoraggio** per un database in SQL Server Management Studio.

## <a name="check-the-status-of-data-migration-in-the-stretch-database-monitor"></a>Controllare lo stato della migrazione dei dati in Monitoraggio Database Estensione
Selezionare **Attività | Estensione | Monitoraggio** per un database in SQL Server Management Studio per aprire Monitoraggio Database Estensione e monitorare la migrazione dei dati.

* La parte superiore della finestra di monitoraggio visualizza informazioni generali sul database di SQL Server abilitato per l'estensione e sul database di Azure remoto.
* Nella parte inferiore della finestra è visualizzato lo stato della migrazione dei dati per ogni tabella del database abilitata per l'estensione.

![Monitoraggio Database Estensione][StretchMonitorImage1]

## <a name="a-namemigrationacheck-the-status-of-data-migration-in-a-dynamic-management-view"></a><a name="Migration"></a>Controllare lo stato della migrazione dei dati in una vista a gestione dinamica
Aprire la DMV **sys.dm\_db\_rda\_migration\_status** per visualizzare il numero di batch e righe di dati migrati. Per altre informazioni, vedere [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Risoluzione dei problemi di migrazione dei dati
**Le righe della tabella abilitata per l'estensione non vengono migrate in Azure. Come mai?**

Esistono diversi problemi che possono influire sulla migrazione. Verificare quanto segue.

* Verificare la connettività di rete per il computer SQL Server.
* Verificare che il firewall di Azure non stia bloccando la connessione di SQL Server all'endpoint remoto.
* Controllare lo stato dell'ultimo batch nella DMV **sys.dm\_db\_rda\_migration\_status**. Se si è verificato un errore, controllare i valori error\_number, error\_state ed error\_severity per il batch.
  
  * Per altre informazioni sulla vista, vedere [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).
  * Per altre informazioni sul contenuto di un messaggio di errore di SQL Server, vedere [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

**Il firewall di Azure blocca le connessioni dal server locale.**

Potrebbe essere necessario aggiungere una regola nelle impostazioni del firewall di Azure per il server Azure per consentire a SQL Server di comunicare con il server Azure remoto.

## <a name="see-also"></a>Vedere anche
[Gestire e risolvere i problemi di Database Estensione](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png



<!--HONumber=Feb17_HO2-->


