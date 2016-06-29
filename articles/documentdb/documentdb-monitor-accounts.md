<properties
	pageTitle="Monitorare le richieste e l'archiviazione di DocumentDB | Microsoft Azure"
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
	ms.date="06/10/2016"
	ms.author="mimig"/>

# Monitorare le richieste, l'uso e l'archiviazione di DocumentDB

È possibile monitorare gli account DocumentDB nel [portale di Microsoft Azure](https://portal.azure.com/). Per ogni account DocumentDB sono disponibili metriche delle prestazioni, quali richieste ed errori del server, e metriche di utilizzo, ad esempio l'utilizzo di risorse di archiviazione.

## Visualizzare le metriche delle prestazioni nel portale 
1.	In una nuova finestra, aprire il [portale di Azure](https://portal.azure.com/) e fare clic su **Sfoglia**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono visualizzare le metriche delle prestazioni.
2.	Per impostazione predefinita, nella sezione **Monitoraggio** è possibile visualizzare:
	*	Richieste totali per il giorno corrente.
	*	Unità richieste totali ([Unità richieste](documentdb-request-units.md)) usate nel giorno corrente.
	*	Spazio di archiviazione usato.

	Se la tabella visualizza **Nessun dato disponibile**, vedere la sezione [Risoluzione dei problemi](#troubleshooting).

	![Schermata della sezione Monitoraggio che mostra le richieste totali del giorno corrente e l'utilizzo delle risorse di archiviazione](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.	Se si fa clic sul riquadro **Richieste**, **Totale UR** o **Archiviazione**, verrà visualizzato un pannello **Metrica** dettagliato.
4.	Il pannello **Metrica** mostra i dettagli relativi alle metriche selezionate. Nella parte superiore del pannello è riportato un grafico delle richieste rilevate su base oraria, mentre sotto il grafico è riportata una tabella che mostra i valori aggregati per le richieste limitate e totali. Nel pannello Metrica viene anche visualizzato l'elenco degli avvisi definiti, filtrato in base alle metriche visualizzate nel pannello Metrica corrente. In questo modo, se sono presenti più avvisi, nel pannello verranno presentati solo quelli pertinenti.   

	![Schermata del pannello Metrica che include le richieste limitate](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## Personalizzare le visualizzazioni delle metriche sulle prestazioni nel portale

1.	Per personalizzare le metriche visualizzate in un grafico specifico, fare clic sul grafico per aprirlo nel pannello **Metrica** e quindi scegliere **Modifica grafico**. ![Schermata dei comandi del pannello Metrica, con Modifica grafico evidenziato](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Nel pannello **Modifica grafico** sono disponibili le opzioni per modificare le metriche visualizzate all'interno del grafico, nonché il relativo intervallo di tempo. ![Schermata del pannello Modifica grafico](./media/documentdb-monitor-accounts/madocdb4.png)

3.	Per modificare le metriche visualizzate nel grafico, selezionare/deselezionare le metriche delle prestazioni disponibili e quindi fare clic su **OK** nella parte inferiore del pannello.
4.	Per modificare l'intervallo di tempo, scegliere un intervallo diverso, ad esempio **Personalizzato** e fare clic su **OK** nella parte inferiore del pannello.  

	![Schermata della parte Intervallo di tempo del pannello Modifica grafico che mostra come immettere un intervallo di tempo personalizzato](./media/documentdb-monitor-accounts/madocdb5.png)


## Creare grafici affiancati nel portale
Il portale di Azure consente di creare grafici di metriche affiancati.

1.	Innanzitutto, fare clic con il pulsante destro del mouse sul grafico dal quale si intende copiare e scegliere **Personalizza**.

	![Schermata del grafico Richieste totali con l'opzione Personalizza evidenziata](./media/documentdb-monitor-accounts/madocdb6.png)

2.	Scegliere **Clona** dal menu per copiare la parte e quindi fare clic su **Personalizzazione completata**.

	![Schermata del grafico Richieste totali con le opzioni Clona e La personalizzazione è completata evidenziate](./media/documentdb-monitor-accounts/madocdb7.png)


Sarà possibile considerare questa parte come tutte le altre parti delle metriche e personalizzare le metriche e l'intervallo di tempo in essa visualizzato. Questa operazione consente di visualizzare contemporaneamente due grafici di metriche affiancati. ![Schermata del grafico Richieste totali e nuovo grafico Richieste totali ora precedente](./media/documentdb-monitor-accounts/madocdb8.png)

## Configurare gli avvisi nel portale
1.	Nel [portale di Azure](https://portal.azure.com/) fare clic su **Sfoglia**, **Account DocumentDB** e quindi fare clic sul nome dell'account DocumentDB per il quale si vogliono configurare gli avvisi relativi alle metriche delle prestazioni.

2.	Se il pannello **Tutte le impostazioni** non è aperto, fare clic su sul comando **Impostazioni** in alto a sinistra per aprire il pannello. ![Screenshot del comando Impostazioni nel pannello Account database](./media/documentdb-monitor-accounts/madocdb10.png)

3.	Nel pannello **Tutte le impostazioni** fare clic su **Regole di avviso** per aprire il relativo pannello. ![Screenshot della parte di regole di avviso selezionata](./media/documentdb-monitor-accounts/madocdb10.5.png)

4.	Nel pannello Regole di avviso fare clic su **Aggiungi avviso**. ![Schermata del pannello Regole di avviso, con il pulsante Aggiungi avviso evidenziato](./media/documentdb-monitor-accounts/madocdb11.png)

5.	Nel pannello **Aggiungi una regola di avviso** specificare:
	*	Il nome della regola di avviso che si sta configurando.
	*	Una descrizione della nuova regola di avviso.
	*	La metrica per la regola di avviso.
	*	Condizione, soglia e periodo per l'attivazione della regola. Ad esempio, un numero di errori del server maggiore di 5 negli ultimi 15 minuti.
	*	Se deve essere inviato un messaggio di posta elettronica all'amministratore del servizio e ai coamministratori quando viene attivata la regola.
	*	Indirizzi di posta elettronica aggiuntivi per le notifiche degli avvisi. ![Schermata del pannello Aggiungi una regola di avviso](./media/documentdb-monitor-accounts/madocdb12.png)

## Monitorare DocumentDB a livello di programmazione
Le metriche a livello account disponibili nel portale, ad esempio l'utilizzo delle risorse di archiviazione dell'account e le richieste totali, non sono disponibili tramite le interfacce API di DocumentDB. È tuttavia possibile recuperare dati relativi all'uso a livello di raccolta tramite le API di DocumentDB. Per recuperare i dati a livello di raccolta, eseguire le operazioni seguenti:

- Per usare l'API REST, [eseguire il comando GET sulla raccolta](https://msdn.microsoft.com/library/mt489073.aspx). Nelle intestazioni x-ms-resource-quota e x-ms-resource-usage della risposta verranno restituite le informazioni relative alla quota e all'utilizzo per la raccolta.
- Per usare .NET SDK, usare il metodo [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) che restituisce un oggetto [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) contenente un certo numero di proprietà d’uso, ad esempio **CollectionSizeUsage**, **DatabaseUsage** e **DocumentUsage**.

Per accedere a metriche aggiuntive, usare l'[SDK di Azure Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights). Le definizioni delle metriche disponibili possono essere recuperate chiamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08 

Le query per recuperare le singole metriche usano il formato seguente:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Per altre informazioni, vedere [Retrieving Resource Metrics via the Azure Insights API](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/) (Recupero delle metriche delle risorse tramite l'API di Azure Insights).

## Risoluzione dei problemi
Se il riquadro di monitoraggio visualizza il messaggio **Nessun dato disponibile** e recentemente si sono eseguite richieste o si sono aggiunti dati al database, è possibile modificare il riquadro in modo da riflettere l'uso recente.

### Modificare un riquadro per aggiornare i dati correnti
1.	Per personalizzare le metriche visualizzate in una parte specifica, fare clic sul grafico per aprire il pannello **Metrica** e quindi scegliere **Modifica grafico**. ![Schermata dei comandi del pannello Metrica, con Modifica grafico evidenziato](./media/documentdb-monitor-accounts/madocdb3.png)

2.	Nel pannello **Modifica grafico** nella sezione **Intervallo di tempo** fare clic su **ora precedente** e quindi su **OK**. ![Schermata del pannello Modifica grafico con l'opzione relativa all'ora precedente selezionata](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.	Il riquadro verrà aggiornato con i dati e l'uso correnti. ![Schermata del riquadro Richieste totali aggiornato all'ora precedente](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## Passaggi successivi
Per altre informazioni sulla capacità di DocumentDB, vedere [Gestire la capacità di DocumentDB](documentdb-manage.md).

<!---HONumber=AcomDC_0615_2016-->