<properties 
	pageTitle="Creare un database nella versione 12 di aggiornamento del database SQL" 
	description="Illustra come creare un database nella versione 12 di aggiornamento del database SQL" 
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
	ms.date="04/28/2015" 
	ms.author="sonalm"/>


# Creare un database nella versione 12 del database SQL


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


[Iscriversi](https://portal.azure.com) alla versione 12 del database SQL [(Anteprima in alcune aree geografiche)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable) per sfruttare i vantaggi della nuova generazione di database SQL in Microsoft Azure. Per iniziare, è necessaria una sottoscrizione a Microsoft Azure. Iscriversi per ottenere una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial) e prendere visione delle informazioni sui [prezzi](http://azure.microsoft.com/pricing/details/sql-database).


| Creazione del database | Screenshot |
| :--- | :--- |
| 1\. Accedere a [http://portal.azure.com/](http://portal.azure.com/). | ![Nuovo portale di Azure][1] |
| 2\. Nella parte inferiore della pagina fare clic su **Nuovo** a sinistra. | ![Avvio di un nuovo servizio][2]|
| 3\. Fare clic su **Database SQL**.| ![Diversi servizi disponibili per la selezione][3] |
| 4\. Viene visualizzato un pannello **Database SQL**. Nel campo **Nome** specificare un nome per il database. | ![Nome del database][4] |
| 5\. Nel pannello **Database SQL** fare clic su **SERVER**. Viene visualizzato un pannello **Server** che presenta due opzioni: creare un nuovo server o usarne uno esistente.| ![Selezione del tipo di server][4] |
|5a. Se si seleziona l'opzione **Utilizza server esistente**, selezionare un server e fare clic su **Seleziona**. Completare quindi tutte le azioni dal passaggio 6 in avanti.| ![Selezione di un server dall'elenco][5]| 
|5b. Se si seleziona **Crea nuovo server**, viene visualizzato il pannello **Nuovo server**. Specificare il nome del server, l'account di accesso amministratore server e la password. Fare clic su **Località** per selezionare l'area geografica in cui si trova il server. | ![Impostazione delle opzioni per la creazione di un nuovo server][9]| 
|5c. Il pannello **Nuovo server** consente di creare il nuovo server con gli aggiornamenti della versione 12. Per altre informazioni sulle funzionalità dei server versione 12, vedere [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md).| ![Selezione del server versione 12][6]|
|5d. Effettuare le selezioni desiderate nel pannello **Nuovo server** e fare clic su **OK**. Viene nuovamente visualizzato il pannello **Database SQL** per completare le azioni necessarie per creare un database. | ![Completamento delle azioni del pannello Nuovo server][8]|
|6\. Fare clic su **Seleziona origine**. I diversi tipi di origini che è possibile selezionare per creare un database sono: un database vuoto, un database di esempio o un backup di un database.| ![Selezione dell'origine per il database][10]|
|7\. Quindi, nel pannello **Database SQL** fare clic su **LIVELLO DI PREZZO**. È possibile selezionare uno dei livelli di prezzo consigliati o **visualizzare tutti** i livelli di prezzo disponibili. Dopo aver scelto il livello desiderato, fare clic su **Seleziona**. <p> Per altre informazioni sui livelli di prezzo, vedere [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](./sql-database-upgrade-new-service-tiers/) e [Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md). |![Selezione di un livello di prezzo][7]
| 8\. Quindi, nel pannello **Database SQL** fare clic su **Configurazione facoltativa**, effettuare le selezioni desiderate e fare clic su **OK**. 
| 9\. Quando si seleziona un server esistente, **Gruppo di risorse** e **Sottoscrizione** sono già impostati automaticamente. Nel pannello **Database SQL** si noterà un'icona di blocco accanto a **Gruppo di risorse** e **Sottoscrizione**. Se si crea un nuovo server, è possibile selezionare o creare un gruppo di risorse. Per altre informazioni, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](resource-group-overview.md).|![Definizione del gruppo di risorse][11]
| 10\. Fare clic su **Crea**. Viene creato un nuovo database con le funzionalità della versione 12 del database SQL. |![Creazione di un nuovo database][12]

## Collegamenti correlati

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 

<!---HONumber=Oct15_HO3-->