<properties
	pageTitle="Monitoraggio e risoluzione dei problemi di migrazione dei dati (Database Estensione) | Microsoft Azure"
	description="Informazioni su come monitorare lo stato della migrazione dei dati."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Monitoraggio e risoluzione dei problemi di migrazione dei dati (Database Estensione)

Per monitorare la migrazione dei dati in Monitoraggio Database Estensione selezionare **Attività | Estensione | Monitoraggio** per un database in SQL Server Management Studio.

## Controllare lo stato della migrazione dei dati in Monitoraggio Database Estensione
Selezionare **Attività | Estensione | Monitoraggio** per un database in SQL Server Management Studio per aprire Monitoraggio Database Estensione e monitorare la migrazione dei dati.

-   La parte superiore della finestra di monitoraggio visualizza informazioni generali sul database di SQL Server abilitato per l'estensione SQL e sul database di Azure remoto.

-   Nella parte inferiore della finestra è visualizzato lo stato della migrazione dei dati per ogni tabella del database abilitata per l'estensione.

![Monitoraggio Database Estensione][StretchMonitorImage1]

## <a name="Migration"></a>Controllare lo stato della migrazione dei dati in una vista a gestione dinamica
Aprire la vista a gestione dinamica **sys.dm\_db\_rda\_migration\_status** per visualizzare il numero di batch e righe di dati migrati. Per altre informazioni, vedere [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="Firewall"></a>Risoluzione dei problemi di migrazione dei dati

**Le righe della tabella con estensione abilitata non vengono migrate in Azure. Come mai?**

Esistono diversi problemi che possono influire sulla migrazione. Verificare quanto segue.

-   Verificare la connettività di rete per il computer SQL Server.

-   Verificare che il firewall di Azure non stia bloccando la connessione di SQL Server all'endpoint remoto.

-   Nella vista a gestione dinamica **sys.dm\_db\_rda\_migration\_status** verificare lo stato dell'ultimo batch. Se si è verificato un errore, controllare i valori error\_number, error\_state e error\_severity per il batch.

    -   Per altre informazioni, vedere [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

    -   Per altre informazioni sul contenuto di un messaggio di errore di SQL Server, vedere [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

**Il firewall di Azure blocca le connessioni dal server locale.**

Potrebbe essere necessario aggiungere una regola nelle impostazioni del firewall di Azure per il server Azure per consentire a SQL Server di comunicare con il server Azure remoto.

## Vedere anche

[Gestire e risolvere i problemi di Database Estensione](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0608_2016-->