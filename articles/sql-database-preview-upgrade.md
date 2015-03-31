<properties 
	pageTitle="Aggiornamento alla versione 12 del database SQL (anteprima)" 
	description="Aggiornamento alla versione 12 del database SQL (anteprima)" 
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
	ms.date="12/11/2014" 
	ms.author="sonalm"/>



# Aggiornamento alla versione 12 del database SQL (anteprima)


[Iscriversi](https://portal.azure.com) all'ultima versione V12 di aggiornamento del database SQL per sfruttare la nuova generazione del database SQL in Microsoft Azure. È necessaria innanzitutto una sottoscrizione a Microsoft Azure. Iscriversi per ottenere una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial) e prendere visione delle informazioni sui [prezzi](http://azure.microsoft.com/pricing/details/sql-database). 

## Come aggiornare un server sul posto con l'aggiornamento più recente del database SQL ##

| Aggiornamento  | Screenshot |
| :--- | :--- |
| 1. Accedere a [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Fare clic su **SFOGLIA**. | ![Browse Services][2] |
| 3.	Fare clic su **SQL Server**. Viene visualizzato un elenco di nomi di server SQL. | ![Select SQL Server service][3] |
| 4. Selezionare il server da copiare in un nuovo server con l'aggiornamento del database SQL abilitato. | ![Shows a list of SQL Servers][4] |
| 5. Fare clic su **Latest SQL Database Update V12**. | ![Latest preview feature][5] |
| 6. Fare clic su **AGGIORNA QUESTO SERVER**. | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **IMPORTANTE**: dopo avere selezionato l'opzione di aggiornamento, il server e i database all'interno di tale server verranno abilitati con le funzionalità della versione V12 di aggiornamento del database SQL e non sarà possibile annullare l'operazione. Per aggiornare i server alla versione V12 di aggiornamento del database SQL, è necessario un livello di servizio Basic, Standard o Premium. Per altre informazioni sui livelli di servizio, vedere [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/).

> **IMPORTANTE**: la replica geografica non è supportata con la versione V12 di aggiornamento del database SQL (anteprima). Per altre informazioni, vedere [Pianificazione e predisposizione dell'aggiornamento all'anteprima V12 del database SQL di Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade).


Dopo avere fatto clic sull'opzione **AGGIORNA QUESTO SERVER**, viene visualizzato un pannello in cui è presente un messaggio relativo a un processo di convalida. 

- Il processo di convalida verifica il livello di servizio del database e controlla se la replica geografica è abilitata. Terminata la convalida, nel pannello vengono presentati i risultati. 
- Al termine del processo di convalida, verrà mostrato un elenco di nomi di database sui quali occorre intervenire per soddisfare i requisiti dell'aggiornamento alla versione V12 del database SQL. **È necessario eseguire le operazioni su ogni database per poter eseguire l'aggiornamento alla versione V12 del database SQL**.
- Quando si fa clic sul nome dei singoli database, viene visualizzato un pannello in cui viene raccomandato un piano tariffario per i servizi in base all'utilizzo corrente. È anche possibile scorrere i vari piani tariffari disponibili e selezionare quello che si ritiene più adatto al proprio ambiente. Tutti i database configurati per la replica geografica devono essere riconfigurati in modo da interrompere la replica. 
- Si noti che la raccomandazione sul piano tariffario non viene visualizzata in assenza di dati sufficienti. 

| Azione | Screenshot |
| :--- | :--- |
| 7. Dopo avere completato la preparazione del server per l'aggiornamento, digitare il nome del server da aggiornare e fare clic su **OK**. | ![Confirm the server name to upgrade][7] |
| 8. Il processo di aggiornamento viene avviato. L'aggiornamento può richiedere fino a 24 ore. Durante questo periodo, tutti i database nel server rimarranno online ma le azioni di gestione del server e del database saranno limitate. Al termine del processo, nel pannello del server viene visualizzato lo stato **Abilitato**. | ![Confirms preview features are enabled][8] |
 

# Collegamenti correlati  #

-  [Novità dell'aggiornamento V12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
