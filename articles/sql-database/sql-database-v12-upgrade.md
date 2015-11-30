<properties 
	pageTitle="Aggiornamento al Database SQL V12 di Azure tramite il portale di anteprima di Azure | Microsoft Azure" 
	description="Viene spiegato come eseguire l'aggiornamento alla versione 12 del Database SQL di Azure, come eseguire l'aggiornamento di database Web e Business e come aggiornare un server versione 11 che sta migrando i database direttamente in un pool di database elastici mediante il portale di anteprima di Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="11/11/2015" 
	ms.author="sstein"/>


# Aggiornamento a Database SQL V12 di Azure tramite il portale di anteprima di Azure


> [AZURE.SELECTOR]
- [Azure preview portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


Database SQL V12 è la versione più recente, pertanto, è consigliabile eseguire l'aggiornamento alla versione 12 del Database SQL. Database SQL V12 presenta molti [vantaggi rispetto alla versione precedente](sql-database-v12-whats-new.md) tra cui:

- Miglioramento della compatibilità con SQL Server.
- Più prestazioni Premium e nuovi livelli di prestazioni.
- [Pool di database elastici](sql-database-elastic-pool.md).

In questo articolo vengono fornite istruzioni per l'aggiornamento di database e server di Database SQL V11 esistenti alla versione 12 di Database SQL.

Durante il processo di aggiornamento alla versione 12 verranno aggiornati tutti i database Web e Business a un nuovo livello di servizio in modo che sono incluse le istruzioni per l'aggiornamento dei database Web e Business.

Inoltre, la migrazione a un [pool di database elastici](sql-database-elastic-pool.md) può essere più conveniente rispetto all'aggiornamento a livelli di prestazioni singoli (livelli di prezzo) per singoli database. I pool inoltre semplificano la gestione dei database perché è sufficiente gestire le impostazioni delle prestazioni per il pool anziché gestire separatamente i livelli di prestazione dei singoli database. Se si dispone di database in più server è consigliabile spostarli nello stesso server e usufruire del vantaggio di metterli in un pool. È possibile [migrare automaticamente e facilmente i database dai server della versione 11 direttamente nel pool di database elastici mediante PowerShell](sql-database-upgrade-server.md). È possibile utilizzare anche il portale per la migrazione dei database della versione V11 in un pool, ma nel portale è necessario avere già un server V12 per creare un pool. Più avanti in questo articolo vengono fornite indicazioni per creare il pool dopo l'aggiornamento del server se si dispone di [database che possono trarre vantaggio da un pool](sql-database-elastic-pool-guidance.md).

Si noti che i database rimarranno online e continueranno a funzionare durante l'operazione di aggiornamento. Al momento della transizione effettiva al nuovo livello di prestazioni, le connessioni al database possono interrompersi temporaneamente per un periodo molto breve che in genere è di circa 90 secondi fino a un massimo di 5 minuti. Se un'applicazione ha una [gestione degli errori temporanei per le interruzioni di connessione](sql-database-connect-central-recommendations.md), è sufficiente impostare la protezione dalle connessioni interrotte alla fine dell'aggiornamento.

L'aggiornamento alla versione 12 del Database SQL non può essere annullato. Dopo un aggiornamento, il server non può essere ripristinato alla versione 11.

Dopo l'aggiornamento alla versione 12, [le indicazioni per i livelli di servizio](sql-database-service-tier-advisor.md) e [le indicazioni per i pool elastici](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier) non saranno immediatamente disponibili fino a quando il servizio non disporrà di tempo per valutare i carichi di lavoro nel nuovo server. La cronologia delle indicazioni del server versione 11 non è applicabile ai server V12 perciò non viene conservata.


## Preparazione dell'aggiornamento

- **Aggiornare tutti i database Web e Business**: vedere la sezione[aggiornare tutti i database Web e Business](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) o utilizzare [PowerShell per aggiornare database e server](sql-database-upgrade-server-powershell.md).
- **Rivedere e sospendere la replica geografica**: se il database SQL di Azure è configurato per la replica geografica è necessario documentare la configurazione attuale e [arrestare la replica geografica](sql-database-geo-replication-portal.md#remove-secondary-database). Al termine dell'aggiornamento, riconfigurare il database per la replica geografica.
- **Aprire queste porte se si dispone di client sulle macchine virtuali di Azure**: se il programma client si connette a SQL Database V12 mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire gli intervalli di porta 11000-11999 e 14000-14999 nella macchina virtuale. Per ulteriori informazioni, vedere [porte per SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).



## Avviare l'aggiornamento

1. Nel [portale di anteprima di Azure](http://portal.azure.com/) individuare il server che si desidera aggiornare selezionando **ESPLORA TUTTO** > **Server SQL**, e selezionando il server desiderato.
2. Selezionare **Ultimo aggiornamento del database SQL**, quindi selezionare **Aggiornamento del server**.

      ![aggiornamento del server][1]

## Aggiornare tutti i database Web e Business.

Se il server dispone di database Web o Business, è necessario aggiornarli. Durante il processo di aggiornamento a SQL Database V12, tutti i database Web e Business verranno aggiornati a un nuovo livello di servizio.

A questo scopo, il servizio database SQL consiglia un livello di servizio e un livello di prestazioni appropriato (piano tariffario) per ogni database. Il servizio consiglia il livello migliore per l'esecuzione del carico di lavoro del database esistente analizzando la cronologia di uso per il database.
    
3. Nel pannello **Aggiorna il server**, selezionare ogni database per esaminare e selezionare il piano tariffario consigliato per l'aggiornamento. È sempre possibile scorrere i piani tariffari disponibili e selezionare quello che si ritiene più adatto al proprio ambiente.


     ![database][2]


7. Dopo aver fatto clic sul livello consigliato, verrà visualizzato il pannello **Scegliere il piano tariffario**, in cui è possibile selezionare un livello e fare clic sul pulsante **Seleziona** per modificare tale livello. Selezionare un nuovo livello per ogni database Web o Business

    È importante tenere presente che i database SQL non sono bloccati in un livello di servizio o di prestazioni specifico e che pertanto è possibile passare facilmente a un altro dei vari livelli di servizio e di prestazioni disponibili quando cambiano i requisiti del database. I database SQL Basic, Standard e Premium vengono infatti fatturati su base oraria e si ha la possibilità di aumentare o ridurre le risorse di ognuno di essi fino a quattro volte nell'arco di un periodo di 24 ore.

    ![raccomandazioni di film][6]


Quando tutti i database nel server sono idonei, si è pronti per iniziare l'aggiornamento

## Confermare l'aggiornamento

3. Quando tutti i database nel server sono idonei per l'aggiornamento, è necessario **DIGITARE IL NOME DEL SERVER** per verificare che si desidera eseguire l'aggiornamento, quindi fare clic su **OK**. 

    ![verifica dell'aggiornamento][3]


4. L'aggiornamento viene avviato e viene visualizzato nella notifica dell’avanzamento. Il processo di aggiornamento viene avviato. L'aggiornamento a V12 può richiedere del tempo, a seconda dei dettagli dei database specifici. Durante questo periodo, tutti i database nel server rimarranno online, ma le azioni di gestione del server e del database saranno limitate.

    ![aggiornamento in corso][4]

    Al momento della transizione effettiva al nuovo livello di prestazioni, le connessioni al database possono interrompersi temporaneamente per un periodo molto breve (in genere, per alcuni secondi). Se un'applicazione ha una gestione degli errori temporanei (logica dei nuovi tentativi) per le interruzioni di connessione, è sufficiente impostare la protezione dalle connessioni interrotte alla fine dell'aggiornamento.

5. Al termine dell'operazione di aggiornamento, nel pannello **Aggiornamento più recente** verrà visualizzato **Abilitato**.

    ![V12 abilitato][5]

## Spostamento di database in un pool di database elastici:

Nel [Portale di anteprima di Azure](https://ms.portal.azure.com/) passare al server V12 e fare clic su **Aggiungere pool**.

-oppure-

Se viene visualizzato un messaggio **Fare clic qui per visualizzare i pool di database elastici consigliati per il server**, fare clic per creare facilmente un pool ottimizzato per i database del server. Per altre informazioni, vedere [Pool di database elastici consigliati](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).

![Aggiungere un pool a un server][7]
   
Seguire le istruzioni presenti nell’articolo [Creare un pool di database elastici](sql-database-elastic-pool.md) per completare la creazione del pool.

## Monitorare i database dopo l'aggiornamento alla versione 12 di Database SQL


Dopo l’aggiornamento, si consiglia di monitorare il database in maniera attiva per assicurarsi che le applicazioni abbiano la prestazione desiderata e ottimizzare l’utilizzo in base alle esigenze.

Oltre al monitoraggio dei singoli database è possibile monitorare i pool di database elastici [tramite il portale](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool) o con [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools)


**Dati sul consumo delle risorse:** per i database Basic, Standard e Premium i dati sul consumo delle risorse sono disponibili tramite il DVM [sys.dm\_ db\_resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) nel database utente. Questa DMV fornisce informazioni sul consumo delle risorse in tempo reale con una granularità di 15 secondi per l'ora precedente di funzionamento. Il consumo percentuale di DTU per un intervallo viene calcolato come consumo percentuale massimo delle dimensioni di CPU, IO e log. Ecco una query per calcolare il consumo percentuale medio di DTU nell'ultima ora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informazioni di monitoraggio aggiuntive:

- [Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerazioni di prezzo e prestazioni per un pool di database flessibile](sql-database=elastic-pool-guidance.md).
- [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)




**Avvisi:** configurare "Avvisi" nel portale di Azure per ricevere una notifica quando il consumo di DTU per un database aggiornato si avvicina a un determinato livello elevato. Nel portale di Azure si possono configurare avvisi di database per diverse metriche delle prestazioni, come DTU, CPU, IO e log. Individuare il database e selezionare **Regole di avviso** nel pannello **Impostazioni**.

Ad esempio, è possibile impostare un avviso di posta elettronica sulla percentuale DTU se il valore medio della percentuale DTU supera il 75% negli ultimi 5 minuti. Fare riferimento a [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) per altre informazioni sulla configurazione di notifiche di avviso.





## Passaggi successivi

- [Controllare i consigli per il pool di database elastici](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).
- [Creare un pool di database elastici](sql-database-elastic-pool-portal.md) e aggiungere alcuni o tutti i database in un pool.
- [Modificare il livello di servizio e il livello delle prestazioni del database](sql-database-scale-up.md).



## Collegamenti correlati

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png

<!---HONumber=Nov15_HO4-->