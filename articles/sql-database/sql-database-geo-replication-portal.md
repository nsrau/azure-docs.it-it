<properties 
    pageTitle="Configurare la replica geografica per il database SQL di Azure con il portale di Azure | Microsoft Azure" 
    description="Configurare la replica geografica per il database SQL di Azure con il portale di Azure" 
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
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# Configurare la replica geografica per il database SQL di Azure con il portale di Azure


> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-replication-overview.md)
- [Portale di Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Questo articolo illustra come configurare la replica geografica attiva per un database SQL usando il [portale di Azure](http://portal.azure.com).

Per avviare un failover con il portale di Azure, vedere [Avviare un failover pianificato o non pianificato per il database SQL di Azure con il portale di Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] La replica geografica attiva (database secondari accessibili in lettura) è ora disponibile per tutti i database in tutti i livelli di servizio. Nell'aprile 2017 il tipo di database secondario non leggibile verrà ritirato e i database non leggibili esistenti verranno aggiornati automaticamente a database secondari accessibili in lettura.

Per configurare la replica geografica tramite il portale di Azure, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure.
- Un database di Database SQL di Azure - Il database primario che si desidera replicare in un'area geografica diversa.

## Aggiungere un database secondario

La procedura seguente crea un nuovo database secondario in una relazione di replica geografica.

Per aggiungere un database secondario è necessario essere il proprietario o un comproprietario della sottoscrizione.

Il database secondario avrà lo stesso nome del database primario e avrà, per impostazione predefinita, lo stesso livello di servizio. Il database secondario può essere accessibile o non accessibile in lettura e può essere un database singolo o un database elastico. Per altre informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md). Dopo aver creato ed effettuato il seeding del database secondario, inizierà la replica dei dati dal database primario al nuovo database secondario.

> [AZURE.NOTE] Se il database partner esiste già, ad esempio come risultato della terminazione di una precedente relazione di replica geografica, il comando non riuscirà.

### Aggiunta del secondario

1. Nel [portale di Azure](http://portal.azure.com) passare al database per cui si vuole installare la replica geografica.
2. Nel pannello del Database SQL, selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Selezionare l'area per creare il database secondario.


    ![Aggiunta del secondario][1]


4. Configurare **Tipo secondario** (**Leggibile** o **Non leggibile**).
5. Selezionare o configurare il server per il database secondario.

    ![Creare il database secondario][3]

5. Facoltativamente, è possibile aggiungere un database secondario a un pool di database elastici:

       - Fare clic su **Pool di database elastici** e selezionare un pool sul server di destinazione in cui creare il database secondario. Un pool deve già esistere nel server di destinazione poiché questo flusso di lavoro non crea un nuovo pool.

6. Fare clic su **Crea** per aggiungere il database secondario.
 
6. Il database secondario viene creato e viene avviato il processo di seeding.
 
    ![seeding][6]

7. Una volta completato il processo di seeding il database secondario visualizza il relativo stato (non leggibile.

    ![database secondario pronto][9]



## Rimuovere il database secondario

L'operazione interrompe in modo permanente la replica al database secondario e modifica il ruolo del database secondario in un database di lettura/scrittura normale. Se la connettività al database secondario viene interrotta il comando ha esito positivo ma il database secondario non diventerà un database di lettura-scrittura fino a quando la connettività non verrà ripristinata.

1. Nel [portale di Azure](http://portal.azure.com) passare al database primario nella relazione di replica geografica.
2. Nel pannello del Database SQL, selezionare **Tutte le impostazioni** > **Replica geografica**.
3. Nell'elenco **SECONDARI** passare al database da rimuovere dalla relazione di replica geografica.
4. Fare clic su **Arresta replica**.

    ![rimuovere database secondario][7]


5. Facendo clic su **Arresta replica** viene visualizzata una finestra di conferma. Fare clic su **Sì** per rimuovere il database dalla relazione di replica geografica e impostarlo come database di lettura/scrittura non incluso in alcuna replica.


    ![confermare la rimozione][8]


## Passaggi successivi

- Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-portal/configure-geo-replication.png
[2]: ./media/sql-database-geo-replication-portal/add-secondary.png
[3]: ./media/sql-database-geo-replication-portal/create-secondary.png
[4]: ./media/sql-database-geo-replication-portal/secondary-type.png
[5]: ./media/sql-database-geo-replication-portal/create.png
[6]: ./media/sql-database-geo-replication-portal/seeding0.png
[7]: ./media/sql-database-geo-replication-portal/remove-secondary.png
[8]: ./media/sql-database-geo-replication-portal/stop-confirm.png
[9]: ./media/sql-database-geo-replication-portal/seeding-complete.png
[10]: ./media/sql-database-geo-replication-portal/failover.png

<!---HONumber=AcomDC_0727_2016-->