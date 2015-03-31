<properties 
	pageTitle="Procedura dettagliata: Attivare l'ultima versione 12 di aggiornamento del database SQL (anteprima)" 
	description="Vengono descritti i passaggi per provare la versione di anteprima 12 del database SQL di Azure usando la nuova interfaccia utente del portale Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard, jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="genemi"/>


# Procedura dettagliata: Attivare l'ultima versione 12 di aggiornamento del database SQL (anteprima)

Questo argomento descrive i passaggi per attivare l'aggiornamento 12 del database SQL di Azure (anteprima), rilasciato da Microsoft nel dicembre 2014.

Per provare l'ultima anteprima della versione 12, è necessario innanzitutto disporre di una sottoscrizione a Microsoft Azure o almeno una sottoscrizione a una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

È possibile attivare l'anteprima della versione 12 usando il nuovo portale di gestione di Microsoft Azure di anteprima all'indirizzo [http://portal.azure.com/](http://portal.azure.com/). Dopo aver attivato l'anteprima della versione 12 per la sottoscrizione, le opzioni di creazione e aggiornamento per l'anteprima della versione 12 vengono sbloccate nel portale Azure. Gli utenti possono quindi avviare il flusso di lavoro di [creazione](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) o [aggiornamento](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) dal pannello del server o del database.

> [AZURE.NOTE]
> Database di test, copie di database o nuovi database sono candidati ideali per l'aggiornamento all'anteprima. Per database di produzione vitali per la propria attività è opportuno attendere il termine del periodo di anteprima.

Per altre informazioni sull'anteprima, vedere [Pianificazione e Predisposizione dell'aggiornamento alla versione 12 del database SQL di Azure (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/).


## A. Autorizzazione di sicurezza

Il primo passaggio consiste nel verificare di disporre di autorizzazioni sufficienti per attivare l'anteprima della versione 12 per la sottoscrizione. Quando si tenta di attivare l'opzione di anteprima della versione 12, viene eseguito un controllo di autorizzazione per verificare che siano disponibili autorizzazioni sufficienti all'interno della sottoscrizione.

 Per provare l'anteprima è necessario disporre di una delle seguenti autorizzazioni:

- Il proprietario della sottoscrizione
- Un coamministratore della sottoscrizione

Per altre informazioni sugli account Azure, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://msdn.microsoft.com/library/hh531793.aspx).

## B. Passaggi nell'interfaccia utente del portale

Questa sezione descrive una sequenza di clic che è possibile seguire una volta nell'interfaccia utente del portale Azure per attivare l'opzione di anteprima della versione 12. Una volta attivata, l'opzione rimane disponibile.

Tutti gli scenari di attivazione usano la stessa idea di base. La prima volta che si tenta di [creare un nuovo server di database SQL](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/), viene visualizzato un pannello denominato **Latest Update (preview)** contenente una casella di controllo che è possibile selezionare per attivare il privilegio per l'uso della versione di anteprima versione 12. Dopo aver attivato il privilegio, la casella di controllo non è più visibile. Viene visualizzato invece un controllo **Sì |No** che consente di specificare se si desidera che il nuovo server usi l'anteprima della versione 12. Se si sceglie **No**, si crea un server versione 11 (come riportato da SELECT @@VERSION;).

### B.1 Controllo Sì|No per la versione di anteprima 12

Dopo aver attivato il privilegio per l'anteprima della versione 12, viene visualizzato il controllo **Sì|No** cerchiato nel seguente screenshot del portale.

![YesNoOptionForTheV12Preview][Image1]


## C. Passaggi successivi

I seguenti argomenti illustrano come usare l'anteprima della versione 12.

- [Creazione di un database nell'aggiornamento alla versione 12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/)
- [Aggiornamento alla versione 12 del database SQL (anteprima)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/)

> [AZURE.NOTE]
> Database di test, copie di database o nuovi database sono candidati ideali per l'aggiornamento all'anteprima. Per database di produzione vitali per la propria attività è opportuno attendere il termine del periodo di anteprima.


<!-- References, Images. -->
[Image1]: ./media/sql-database-preview-sign-up/V12Preview-YesNo-Option-New-SQLDatabase-Server-Newserver-Screenshot-e23.png


<!-- EOF -->

<!--HONumber=47-->
