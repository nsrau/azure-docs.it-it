<properties 
	pageTitle="Creazione di un database nell'aggiornamento alla versione 12 del database SQL (anteprima)" 
	description="Creazione di un database nell'aggiornamento alla versione 12 del database SQL (anteprima)" 
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


# Creazione di un database nell'aggiornamento alla versione 12 del database SQL (anteprima)

[Iscriversi](https://portal.azure.com) all'ultima versione 12 di aggiornamento del database SQL per sfruttare la nuova generazione del database SQL in Microsoft Azure. Per iniziare, è necessaria una sottoscrizione a Microsoft Azure. Iscriversi per ottenere una [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial) e prendere visione delle informazioni sui [prezzi](http://azure.microsoft.com/pricing/details/sql-database). 


| Creazione del database | Screenshot |
| :--- | :--- |
| 1. Accedere a [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Nella parte inferiore della pagina fare clic su **Nuovo**. | ![Initiate New service][2]|
| 3. Fare clic su **Database SQL**.| ![Different services to select from][3] |
| 4. Viene visualizzato un pannello **Database SQL**. Nel campo **Nome** specificare un nome di database. | ![Name the database][4] |
| 5. Nel pannello **Database SQL** fare clic su **SERVER**. Viene visualizzato un pannello **Server** che presenta due opzioni: creare un nuovo server o usare un server esistente.| ![select type of server][4] |
|5a. Se si seleziona l'opzione **Utilizza server esistente**, selezionare un server e fare clic su **Seleziona**. Completare quindi tutte le azioni dal passaggio 6 in avanti.| ![select a server from the list][5]| 
|5b.   Se si seleziona **Create a new server**, si apre il pannello **Nuovo server**. Specificare il nome del server, l'account di accesso amministratore server e la password. Fare clic su **Percorso**   per selezionare il percorso del server. | ![Complete create new server options][9]| 
|5c.Il pannello **Nuovo server** consente di creare il nuovo server con gli aggiornamenti 12. Per altre informazioni sulle funzionalità dei server 12, vedere [Novità del database SQL di Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/).| ![Select V12 server][6]|
|5d. Effettuare le selezioni desiderate nel pannello **Nuovo server** e fare clic su **OK**. Si tornerà al pannello **Database SQL** per completare il resto delle azioni per la creazione di un database. | ![Complete New Server blade actions][8]|
|6. Fare clic su **Select Source**. I diversi tipi di origini che è possibile selezionare per creare un database sono: un database vuoto, un database di esempio o un backup di un database.| ![Select the source for the database][10]|
|7. Quindi, nel pannello **Database SQL** fare clic su **PRICING TIER**. È possibile selezionare il piano tariffario consigliato o scorrere tutti i piani tariffari disponibili. Dopo avere effettuato una scelta, fare clic su **Seleziona**. <p> Per altre informazioni sui livelli di prezzo, vedere [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) e [Livelli di servizio e livelli di prestazioni del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx). |![Select a pricing tier][7]
| 8. Successivamente, nel pannello **Database SQL** fare clic su **Optional Configuration**, effettuare le selezioni e fare clic su **OK**. 
| 9. Nel pannello **Database SQL** fare clic su **Resource Group**. Creare un nuovo gruppo di risorse o selezionarne uno esistente dall'elenco. Fare clic su **OK**. Per altre informazioni, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups).|![Specify Resource group][11]
| 10. Nel pannello **Database SQL** fare clic su **SOTTOSCRIZIONE**. Si apre un pannello **Sottoscrizione**. Selezionare una sottoscrizione Azure che supporta la selezione di server effettuata. Si noti che in base alla selezione della sottoscrizione, le impostazioni del server potrebbero cambiare.| ![Select subscription.][13]
| 11. Fare clic su **Crea**. Viene creato un nuovo database con le funzionalità della versione 12 di aggiornamento del database SQL. |![Creates a new database][12]

# Collegamenti correlati  #

-  [Novità del database SQL di Azure](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Pianificazione e predisposizione dell'aggiornamento alla versione 12 del database SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
 