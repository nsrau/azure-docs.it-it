<properties 
	pageTitle="Monitorare un account DocumentDB usando il portale di Azure | Microsoft Azure" 
	description="Informazioni su come monitorare l'account DocumentDB per le metriche relative alle prestazioni, ad esempio richieste ed errori del server, e all'utilizzo, ad esempio l'utilizzo di risorse di archiviazione." 
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
	ms.date="02/18/2016" 
	ms.author="mimig"/>

# Monitorare l'uso di un account DocumentDB nel portale di Azure

È possibile monitorare gli account DocumentDB nel [portale di Microsoft Azure](https://portal.azure.com/). Per ogni account DocumentDB sono disponibili metriche delle prestazioni, quali richieste ed errori del server, e metriche di utilizzo, ad esempio l'utilizzo di risorse di archiviazione.

## Visualizzare le metriche delle prestazioni per un account DocumentDB
1.	Nel [portale di Azure](https://portal.azure.com/) fare clic su **Sfoglia**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono visualizzare le metriche delle prestazioni.
2.	Per impostazione predefinita, nella sezione **Monitoraggio** è possibile visualizzare:
	*	Richieste totali per il giorno corrente.
	*	Utilizzo delle risorse di archiviazione per l'account. 

	Se la tabella visualizza **Nessun dato disponibile**, vedere la sezione [Risoluzione dei problemi](#troubleshooting).
	
	![Schermata della sezione Monitoraggio che mostra le richieste totali del giorno corrente e l'utilizzo delle risorse di archiviazione](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	Se si fa clic sul riquadro **Richieste totali** o **Archiviazione**, verrà visualizzato un pannello **Metrica** dettagliato.
4.	Il pannello **Metrica** mostra i dettagli relativi alle metriche selezionate. Nella parte superiore del pannello è riportato un grafico, sotto il quale viene visualizzata una tabella contenente i valori aggregati delle metriche selezionate, ad esempio medio, minimo, massimo e totale. Nel pannello Metrica viene anche visualizzato l'elenco degli avvisi definiti, filtrato in base alle metriche visualizzate nel pannello Metrica corrente. In questo modo, se sono presenti più avvisi, nel pannello verranno presentati solo quelli pertinenti.   

	![Schermata del pannello Metrica](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## Personalizzare le visualizzazioni delle metriche delle prestazioni per un account DocumentDB

1.	Per personalizzare le metriche visualizzate in una parte specifica, fare clic con il pulsante destro del mouse sul grafico della metrica e quindi scegliere **Modifica grafico**. ![Schermata del grafico Richieste totali con il pulsante Modifica grafico evidenziato](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Nel pannello **Modifica grafico** sono disponibili le opzioni per modificare le metriche visualizzate nella parte, nonché il relativo intervallo di tempo. ![Schermata del pannello Modifica grafico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Per modificare le metriche visualizzate nella parte, selezionare/deselezionare le metriche delle prestazioni disponibili e quindi fare clic su **Salva** nella parte inferiore del pannello.
4.	Per modificare l'intervallo di tempo, scegliere un intervallo diverso, ad esempio **Personalizzato** e fare clic su **Salva** nella parte inferiore del pannello.  

	![Schermata della parte Intervallo di tempo del pannello Modifica grafico che mostra come immettere un intervallo di tempo personalizzato](./media/documentdb-monitor-accounts/madocdb5.png)


## Creare grafici di metriche delle prestazioni affiancati
Il portale di Azure consente di creare grafici di metriche affiancati.

1.	Fare prima di tutto clic con il pulsante destro del mouse sul grafico da clonare e modificare e quindi scegliere **Personalizza**. 

	![Schermata del grafico Richieste totali con l'opzione Personalizza evidenziata](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Scegliere **Clona** dal menu per copiare la parte e quindi fare clic su **Personalizzazione completata**.

	![Schermata del grafico Richieste totali con le opzioni Clona e La personalizzazione è completata evidenziate](./media/documentdb-monitor-accounts/madocdb7.png)


Sarà possibile considerare questa parte come tutte le altre parti delle metriche e personalizzare le metriche e l'intervallo di tempo in essa visualizzato. Questa operazione consente di visualizzare contemporaneamente due grafici di metriche affiancati. ![Schermata del grafico Richieste totali e nuovo grafico Richieste totali ora precedente](./media/documentdb-monitor-accounts/madocdb8.png)

## Visualizzare le metriche di utilizzo per un account DocumentDB
1.	Nel [portale di Azure](https://portal.azure.com/) fare clic su **Sfoglia**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono visualizzare le metriche di utilizzo.
2.	Per impostazione predefinita, nella sezione **Utilizzo** è possibile visualizzare:
	*	Costo stimato alla data dell'account DocumentDB per il periodo di fatturazione corrente.
	*	Spazio di archiviazione usato nell'account.
	*	Spazio di archiviazione disponibile massimo dell'account (soglia).
	*	Utilizzo di utenti e autorizzazioni.
	*	Utilizzo di allegati.

	![Schermata della sezione Utilizzo](./media/documentdb-monitor-accounts/madocdb9.png)
 
## Configurare gli avvisi delle metriche delle prestazioni per un account DocumentDB
1.	Nel [portale di Azure](https://portal.azure.com/) fare clic su **Esplora tutto**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono configurare gli avvisi relativi alle metriche delle prestazioni.
2.	Nella sezione **Operazioni** fare clic sulla parte **Regole di avviso**. ![Schermata della sezione Operazioni, con la parte Regole di avviso selezionata](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Nel pannello Regole di avviso fare clic su **Aggiungi avviso**. ![Schermata del pannello Regole di avviso, con il pulsante Aggiungi avviso evidenziato](./media/documentdb-monitor-accounts/madocdb11.png)

4.	Nel pannello **Aggiungi una regola di avviso** specificare:
	*	Il nome della regola di avviso che si sta configurando.
	*	Una descrizione della nuova regola di avviso.
	*	La metrica per la regola di avviso.
	*	Condizione, soglia e periodo per l'attivazione della regola. Ad esempio, un numero di errori del server maggiore di 5 negli ultimi 15 minuti.
	*	Se deve essere inviato un messaggio di posta elettronica all'amministratore del servizio e ai coamministratori quando viene attivata la regola.
	*	Indirizzi di posta elettronica aggiuntivi per le notifiche degli avvisi. ![Schermata del pannello Aggiungi una regola di avviso](./media/documentdb-monitor-accounts/madocdb12.png)

## Altri modi per monitorare DocumentDB
Le metriche a livello account disponibili nel portale, ad esempio l'utilizzo delle risorse di archiviazione dell'account e le richieste totali, non sono disponibili tramite le interfacce API di DocumentDB. È tuttavia possibile recuperare dati relativi all'utilizzo a livello di raccolta. Per recuperare i dati a livello di raccolta, eseguire le operazioni seguenti:

- Per usare l'API REST, [eseguire il comando GET sulla raccolta](https://msdn.microsoft.com/library/mt489073.aspx). Nelle intestazioni x-ms-resource-quota e x-ms-resource-usage della risposta verranno restituite le informazioni relative alla quota e all'utilizzo per la raccolta.
- Con .NET SDK, usare il metodo [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), che restituisce un oggetto [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) contenente un certo numero di proprietà di utilizzo, ad esempio **CollectionSizeUsage**, **DatabaseUsage** e **DocumentUsage**.

## Risoluzione dei problemi
Se un riquadro di monitoraggio visualizza il messaggio **Nessun dato disponibile** e recentemente si sono eseguite richieste o si sono aggiunti dati al database, è possibile modificare il riquadro in modo da riflettere l'utilizzo recente.

### Modificare un riquadro per aggiornare i dati correnti
1.	Per personalizzare le metriche visualizzate in una parte specifica, fare clic con il pulsante destro del mouse sul grafico della metrica e quindi scegliere **Modifica grafico**. ![Schermata del riquadro Richieste totali che indica che non ci sono dati disponibili](./media/documentdb-monitor-accounts/documentdb-no-available-data.png)

2.	Nel pannello **Modifica grafico**, nella sezione **Intervallo di tempo**, fare clic su **ora precedente** e quindi su **Salva**. ![Schermata del pannello Modifica grafico con l'opzione relativa all'ora precedente selezionata](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	Il riquadro verrà aggiornato con i dati e l'utilizzo correnti. ![Schermata del riquadro Richieste totali aggiornato all'ora precedente](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## Passaggi successivi
Per altre informazioni sulla capacità di DocumentDB, vedere [Gestire la capacità di DocumentDB](documentdb-manage.md).
 

<!---HONumber=AcomDC_0224_2016-->