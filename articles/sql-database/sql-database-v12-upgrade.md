<properties 
	pageTitle="Eseguire l'aggiornamento alla versione 12 del database SQL" 
	description="Viene illustrato come eseguire l'aggiornamento alla versione 12 del database SQL di Azure da una versione precedente del medesimo database." 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="05/15/2015" 
	ms.author="sonalm"/>


# Eseguire l'aggiornamento a SQL Database V12 in locale


[Iscriversi](https://portal.azure.com) alla versione 12 del database SQL per sfruttare l'ultima generazione del database SQL in Microsoft Azure. È necessaria innanzitutto una sottoscrizione a Microsoft Azure. Iscriversi per ottenere una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial) e prendere visione delle informazioni sui [prezzi](http://azure.microsoft.com/pricing/details/sql-database).


## Passaggi per eseguire l'aggiornamento alla versione 12 del database SQL


| Passaggi dell'aggiornamento | Screenshot |
| :--- | :--- |
| 1\. Accedere a [http://portal.azure.com/](http://portal.azure.com/). | ![Nuovo portale di Azure][1] |
| 2\. Fare clic su **SFOGLIA**. | ![Individuare i servizi][2] |
| 3\. Fare clic su **SQL Server**. Viene visualizzato un elenco di nomi di server SQL. | ![Selezionare il servizio SQL Server][3] |
| 4\. Selezionare il server da copiare in un nuovo server con l'aggiornamento del database SQL abilitato. | ![Mostra un elenco di istanze di SQL Server][4] |
| 5\. Fare clic su **Impostazioni** o **Versione server** per aggiornare il server alla versione 12. | ![Funzionalità di anteprima più recenti][5] |
| 6\. Fare clic su **AGGIORNA QUESTO SERVER**. | ![Aggiornamenti di SQL Server per l'anteprima][6] |


> [AZURE.NOTE]Dopo avere selezionato l'opzione di aggiornamento, il server e i database all'interno di tale server verranno abilitati con le funzionalità della versione 12 del database SQL e non sarà possibile annullare l'operazione. Per aggiornare i server alla versione 12 del database SQL, è necessario disporre di un livello di servizio Basic, Standard o Premium. Per ulteriori informazioni sui livelli di servizio, vedere [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-new-service-tiers.md).


> [AZURE.IMPORTANT]La replica geografica non è supportata con la versione 12 del database SQL \(anteprima\). Per ulteriori informazioni, vedere [Pianificazione e predisposizione dell'aggiornamento all'anteprima V12 del database SQL di Azure](sql-database-v12-plan-prepare-upgrade.md).


Dopo avere fatto clic sull'opzione **AGGIORNA QUESTO SERVER**, viene visualizzato un pannello in cui è presente un messaggio relativo a un processo di convalida.


- Il processo di convalida verifica il livello di servizio del database e controlla se la replica geografica è abilitata. Terminata la convalida, nel pannello vengono presentati i risultati. 
- Al termine del processo di convalida, verrà mostrato un elenco di nomi di database sui quali occorre intervenire per soddisfare i requisiti di aggiornamento alla versione 12 del database SQL.
 - **È necessario eseguire le operazioni su ogni database per poter eseguire l'aggiornamento alla versione 12 del database SQL**.
- Quando si fa clic sul nome dei singoli database, viene visualizzato un pannello in cui viene raccomandato un piano tariffario per i servizi in base all'utilizzo corrente. È anche possibile scorrere i vari piani tariffari disponibili e selezionare quello che si ritiene più adatto al proprio ambiente. Tutti i database configurati per la replica geografica devono essere riconfigurati in modo da interrompere la replica. 
- Si noti che la raccomandazione sul piano tariffario non viene visualizzata in assenza di dati sufficienti. 


| Azione | Screenshot |
| :--- | :--- |
| 7\. Dopo avere completato la preparazione del server per l'aggiornamento, digitare il nome del server da aggiornare e fare clic su **OK**. | ![Verificare il nome del server per eseguire l'aggiornamento][7] |
| 8\. Il processo di aggiornamento viene avviato. L'aggiornamento può richiedere fino a 24 ore. Durante questo periodo, tutti i database nel server rimarranno online ma le azioni di gestione del server e del database saranno limitate. Al termine del processo, nel pannello del server viene visualizzato lo stato **Abilitato**. | ![Verifica che siano abilitate le funzionalità di anteprima][8] |


## Cmdlet di PowerShell


I cmdlet di PowerShell consentono di avviare, arrestare o monitorare un aggiornamento alla versione 12 del database SQL di Azure dalla versione 11 o qualsiasi altra versione precedente alla 12.


Per la documentazione di riferimento sui cmdlet di Powershell, vedere:


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


Il cmdlet Stop comporta l'annullamento non la sospensione. Non è possibile riprendere un aggiornamento, è possibile solamente riavviarlo dall'inizio. Il cmdlet Stop libera e rilascia tutte le risorse appropriate.


## Collegamenti correlati

-  [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md) 
- [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/firstscreenportal.png
[2]: ./media/sql-database-v12-upgrade/firstscreenportal.png
[3]: ./media/sql-database-v12-upgrade/sqlserverlist.png
[4]: ./media/sql-database-v12-upgrade/sqlserverlist.png
[5]: ./media/sql-database-v12-upgrade/latestprview.png
[6]: ./media/sql-database-v12-upgrade/upgrade.png
[7]: ./media/sql-database-v12-upgrade/typeservername.png
[8]: ./media/sql-database-v12-upgrade/enabled.png
 

<!---HONumber=58_postMigration-->