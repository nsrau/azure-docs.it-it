<properties 
    pageTitle="Avviare un failover pianificato o non pianificato per il database SQL di Azure con il portale di Azure | Microsoft Azure" 
    description="Avviare un failover pianificato o non pianificato per il database SQL di Azure usando il portale di Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="07/19/2016"
    ms.author="sstein"/>

# Avviare un failover pianificato o non pianificato per il database SQL di Azure con il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Questo articolo illustra come avviare il failover su un database SQL secondario con il [portale di Azure](http://portal.azure.com). Per configurare la replica geografica, vedere [Configurare la replica geografica per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-portal.md).


## Avviare un failover

Il database secondario può diventare il database primario.

1. Nel [portale di Azure](http://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nel pannello del Database SQL, selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Nell’elenco **SECONDARI**, selezionare il database che si vuole usare come nuovo database primario e fare clic su **Failover**.

    ![failover][2]

4. Fare clic su **Sì** per avviare il failover.

Il comando passa immediatamente il database secondario al ruolo di database primario.

Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Se il database primario dispone di più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali.

>[AZURE.NOTE] Se il database primario è online sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.


## Risorse aggiuntive   


- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/) (Nuove funzionalità di replica geografica in evidenza)
- [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0720_2016-->