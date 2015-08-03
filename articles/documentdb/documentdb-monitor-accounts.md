<properties 
	pageTitle="Monitorare un account DocumentDB | Azure" 
	description="Informazioni su come monitorare l'account DocumentDB per le metriche relative alle prestazioni (ad esempio richieste ed errori del server) e all'utilizzo (ad esempio il consumo di memoria)." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="mimig"/>

#Monitorare un account DocumentDB  

È possibile monitorare gli account di DocumentDB nel [portale di anteprima di Microsoft Azure](https://portal.azure.com/). Per ogni account DocumentDB sono disponibili le metriche delle prestazioni (errori delle richieste e del server) e le metriche di utilizzo (utilizzo dell'archiviazione).

## <a id="metrics"></a> Come visualizzare le metriche delle prestazioni per un account DocumentDB
1.	Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Sfoglia**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono visualizzare le metriche delle prestazioni.
2.	Per impostazione predefinita, nella sezione **Monitoraggio** è possibile visualizzare:
	*	Richieste totali per il giorno corrente.
	*	Media delle richieste al secondo per il giorno corrente. 
	
	![Schermata della sezione Monitoraggio](./media/documentdb-monitor-accounts/madocdb1.png)


3.	Se si fa clic sulla parte **Richieste totali** o **Media richieste al secondo**, verrà visualizzato il pannello **Metrica** dettagliato.
4.	Il pannello **Metrica** mostra i dettagli relativi alle metriche selezionate. Nella parte superiore del pannello è riportato un grafico, sotto il quale viene visualizzata una tabella contenente i valori aggregati delle metriche selezionate, ad esempio media, minimo e massimo. Nel pannello Metrica viene anche visualizzato l'elenco degli avvisi definiti, filtrato in base alle metriche visualizzate nel pannello Metrica corrente. In questo modo, se sono presenti più avvisi, nel pannello verranno presentati solo quelli pertinenti.   

	![Schermata del pannello Metrica](./media/documentdb-monitor-accounts/madocdb2.png)


## <a id="custom"></a>Personalizzare le visualizzazioni delle metriche delle prestazioni per un account DocumentDB

1.	Per personalizzare le metriche visualizzate in una parte specifica, fare clic con il pulsante destro del mouse sul grafico della metrica e quindi scegliere **Modifica grafico**. ![Schermata del grafico Richieste totali con il pulsante Modifica grafico evidenziato](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Nel pannello **Modifica grafico** sono disponibili le opzioni per modificare le metriche visualizzate nella parte, nonché il relativo intervallo di tempo. ![Schermata del pannello Modifica grafico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Per modificare le metriche visualizzate nella parte, selezionare/deselezionare le metriche delle prestazioni disponibili e quindi fare clic su **Salva** nella parte inferiore del pannello.
4.	Per apportare cambiamenti all'intervallo di tempo è sufficiente scegliere un intervallo diverso, ad esempio **Ora precedente** e fare clic su **Salva** nella parte inferiore del pannello.  

	![Schermata della parte Intervallo di tempo del pannello Modifica grafico che mostra come immettere un intervallo di tempo personalizzato](./media/documentdb-monitor-accounts/madocdb5.png)


## <a id="create"></a>Creare grafici di metriche delle prestazioni affiancati
Il portale di anteprima di Azure consente di creare grafici di metriche affiancati.

1.	Fare prima di tutto clic con il pulsante destro del mouse sul grafico da clonare e modificare e quindi scegliere **Personalizza**. 

	![Schermata del grafico Richieste totali con l'opzione Personalizza evidenziata](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Scegliere **Clona** dal menu per copiare la parte e quindi fare clic su **Personalizzazione completata**.

	![Schermata del grafico Richieste totali con le opzioni Clona e La personalizzazione è completata evidenziate](./media/documentdb-monitor-accounts/madocdb7.png)


Sarà possibile considerare questa parte come tutte le altre parti delle metriche e personalizzare le metriche e l'intervallo di tempo in essa visualizzato. Questa operazione consente di visualizzare contemporaneamente due grafici di metriche affiancati. ![Schermata del grafico Richieste totali e nuovo grafico Richieste totali ora precedente](./media/documentdb-monitor-accounts/madocdb8.png)

## <a id="view"></a>Visualizzare le metriche di utilizzo per un account DocumentDB
1.	Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Sfoglia**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono visualizzare le metriche di utilizzo.
2.	Per impostazione predefinita, nella sezione **Utilizzo** è possibile visualizzare:
	*	Costo stimato.
	*	Spazio di archiviazione usato nell'account.
	*	Spazio di archiviazione disponibile massimo dell'account.
	*	Utilizzo di utenti e autorizzazioni.
	*	Utilizzo di allegati.

	![Schermata della sezione Utilizzo](./media/documentdb-monitor-accounts/madocdb9.png)
 
## <a id="setup"></a>Configurare gli avvisi delle metriche delle prestazioni per un account DocumentDB
1.	Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Sfoglia**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono configurare gli avvisi relativi alle metriche delle prestazioni.
2.	Nella sezione **Operazioni** fare clic sulla parte **Regole di avviso**. ![Schermata della sezione Operazioni, con la parte Regole di avviso selezionata](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Nel pannello Regole di avviso fare clic su **Aggiungi avviso**. ![Schermata del pannello Regole di avviso, con il pulsante Aggiungi avviso evidenziato](./media/documentdb-monitor-accounts/madocdb11.png)

4.	Nel pannello **Aggiungi una regola di avviso** specificare:
	*	Il nome della regola di avviso che si sta configurando.
	*	Una descrizione della nuova regola di avviso.
	*	La metrica per la regola di avviso.
	*	Condizione, soglia e periodo per l'attivazione della regola. Ad esempio, un numero di errori del server maggiore di 5 negli ultimi 15 minuti.
	*	Se deve essere inviato un messaggio di posta elettronica all'amministratore del servizio e ai coamministratori quando viene attivata la regola.
	*	Indirizzi di posta elettronica aggiuntivi per le notifiche degli avvisi. ![Schermata del pannello Aggiungi una regola di avviso](./media/documentdb-monitor-accounts/madocdb12.png)

 
## <a id="next"></a>Passaggi successivi
Per altre informazioni sulla capacità di DocumentDB, vedere [Gestire la capacità di DocumentDB](documentdb-manage.md).

<!--Anchors-->
[How to view performance metrics for a DocumentDB account]: #How-to-view-performance-metrics-for-a-DocumentDB-account
[Customize performance metric views for a DocumentDB account]: #Customize-performance-metric-views-for-a-DocumentDB-account
[How to create side-by-side performance metric charts]: #How-to-create-side-by-side-performance-metric-charts
[How to view usage metrics for a DocumentDB account]: #How-to-view-usage-metrics-for-a-DocumentDB-account
[How to setup performance metric alerts for a DocumentDB account]: #How-to-setup-performance-metric-alerts-for-a-DocumentDB-account
[Next steps]: #Next-steps
 

<!---HONumber=July15_HO4-->