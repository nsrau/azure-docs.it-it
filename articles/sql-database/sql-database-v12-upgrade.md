<properties 
	pageTitle="Eseguire l'aggiornamento alla versione 12 del database SQL" 
	description="Viene illustrato come eseguire l'aggiornamento alla versione 12 del database SQL di Azure da una versione precedente del medesimo database." 
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
	ms.date="09/30/2015" 
	ms.author="sstein"/>


# Eseguire l'aggiornamento alla versione 12 del database SQL


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


SQL Database V12 è la versione più recente del database SQL e ha [molti vantaggi rispetto alla versione V2 precedente](sql-database-v12-whats-new.md). In questo articolo viene illustrato come aggiornare i server V2 in V12 tramite il portale di anteprima di Azure.

Durante il processo di aggiornamento a SQL Database V12, è inoltre necessario [aggiornare tutti i database Web e Business a un nuovo livello di servizio](sql-database-upgrade-new-service-tiers.md). Le indicazioni riportate di seguito includono i passaggi per aggiornare i database Web e Business con indicazioni del piano tariffario in base all'utilizzo cronologico del database.



1. Nel [portale di anteprima di Azure](http://portal.azure.com/) individuare il server che si desidera aggiornare selezionando **ESPLORA TUTTO** > **Server SQL**, e selezionando il server desiderato.
2. Selezionare **Ultimo aggiornamento del database SQL**, quindi selezionare **Aggiornamento del server**.

      ![aggiornamento del server][1]

## Aggiornare database Web e Business

2. Aggiornare tutti i database Web e Business. Se il server dispone di database Web o Business, è necessario aggiornarli. A questo scopo, il servizio database SQL consiglia un livello di servizio e un livello di prestazioni appropriato (piano tariffario) per ogni database. Analizzando l'utilizzo cronologico di ogni database, il servizio consiglia un livello più adatto per l'esecuzione del carico di lavoro del database esistente. 
    
    Selezionare ogni database per esaminare e selezionare il piano tariffario consigliato per l'aggiornamento. È sempre possibile scorrere i piani tariffari disponibili e selezionare quello che si ritiene più adatto al proprio ambiente.

     ![database][2]



7. Dopo aver fatto clic sul livello consigliato, verrà visualizzato il pannello **Scegliere il piano tariffario**, in cui è possibile selezionare un livello e fare clic sul pulsante **Seleziona** per modificare tale livello. Selezionare un nuovo livello per ogni database Web o Business

    ![raccomandazioni di film][6]


Quando tutti i database nel server sono idonei, si è pronti per iniziare l'aggiornamento

## Avviare l'aggiornamento

3. Quando tutti i database nel server sono idonei per l'aggiornamento, è necessario **DIGITARE IL NOME DEL SERVER** per verificare che si desidera eseguire l'aggiornamento, quindi fare clic su **OK**. 

    ![verifica dell'aggiornamento][3]


4. L'aggiornamento viene avviato e viene visualizzato nella notifica dell’avanzamento. Il processo di aggiornamento viene avviato. L'aggiornamento a V12 può richiedere del tempo, a seconda dei dettagli dei database specifici. Durante questo periodo, tutti i database nel server rimarranno online, ma le azioni di gestione del server e del database saranno limitate.

    ![aggiornamento in corso][4]

    Al momento della transizione effettiva al nuovo livello di prestazioni, le connessioni al database possono interrompersi temporaneamente per un periodo molto breve (in genere, per alcuni secondi). Se un'applicazione ha una gestione degli errori temporanei (logica dei nuovi tentativi) per le interruzioni di connessione, è sufficiente impostare la protezione dalle connessioni interrotte alla fine dell'aggiornamento.

5. Al termine dell'operazione di aggiornamento, le funzionalità del server SQL Database V12 vengono abilitate.

    ![V12 abilitato][5]


## Collegamenti correlati

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=Oct15_HO1-->