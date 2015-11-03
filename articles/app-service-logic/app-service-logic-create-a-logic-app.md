<properties
	pageTitle="Creare un'app per la logica | Microsoft Azure"
	description="Informazioni su come creare un'app per la logica che connette servizi SaaS"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/21/2015"
	ms.author="stepsic"/>

# Creare una nuova app per la logica che connette servizi SaaS

| Riferimento rapido |
| --------------- |
| [Linguaggio di definizione delle app per la logica](https://msdn.microsoft.com/library/azure/dn948512.aspx?f=255&MSPPError=-2147217396) |
| [Documentazione del connettore delle app per la logica](https://azure.microsoft.com/it-IT/documentation/articles/app-service-logic-connectors-list/) |
| [Forum delle app per la logica](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=azurelogicapps) |

Questo argomento illustra come iniziare in pochi minuti a usare le [app per la logica del servizio app](app-service-logic-what-are-logic-apps.md). Verrà illustrato un flusso di lavoro che consente di inviare un set di tweet a cui si è interessati a una cartella Dropbox.

Per usare questo scenario è necessario disporre di:

- Una sottoscrizione di Azure
- Un account Twitter
- Un account Dropbox

<!--- TODO: Add try it now information here -->

## Ottenere i connettori

Prima di tutto, è necessario creare i due connettori che verranno usati: [Dropbox Connector](app-service-logic-connector-dropbox.md) e [Twitter Connector](app-service-logic-connector-twitter.md). A causa delle restrizioni relative all'API Twitter, sarà necessario registrarsi anche per un'app gratuita in Twitter. A questo scopo:

1. Accedere al portale di Azure.

2. Fare clic su [Marketplace](https://portal.azure.com/#blade/HubsExtension/GalleryFeaturedMenuItemBlade/selectedMenuItemId/apiapps) nella schermata iniziale e cercare Twitter oppure [fare clic qui](https://portal.azure.com/#create/microsoft_com.TwitterConnector.0.2.2).

3. Selezionare **Twitter Connector** e fare clic su **Crea**. Verrà visualizzata una panoramica di tutte le impostazioni. È possibile mantenere il nome **Twitter Connector**.
4. Selezionare **Impostazioni pacchetto**. Qui si dovranno immettere le informazioni dall'applicazione Twitter. È possibile configurare un'applicazione gratuita in tre passaggi:
	1. Accedere alla [pagina per la registrazione di app Twitter](http://apps.twitter.com).
	2. Creare una nuova app.
	3. Assegnare all'app un nome e una descrizione. È possibile immettere un URL qualsiasi per il sito Web e per l'URL di callback (non lasciare il campo vuoto).
	4. Dopo aver completato la registrazione, copiare il valore di **Consumer Key** da Twitter nel campo **clientId** in Azure e il valore **Consumer Secret** da Twitter nel campo **clientSecret**.
	5. Fare clic su **OK** nel riquadro Azure per tornare alle altre impostazioni dell'API.

5. Digitare un nome di piano in **Crea nuovo piano di servizio app**.

	>[AZURE.NOTE]I passaggi in questa sezione presuppongono che si stia creando un nuovo piano di servizio app. Se si usa un piano di servizio app esistente, fare clic su **Seleziona esistente**, selezionare il piano esistente e procedere all'ultimo passaggio di questa sezione. È necessario un piano per ospitare tutte le app.

6.  Selezionare un **Piano tariffario** per il nuovo piano.

	>[AZURE.NOTE]Per impostazione predefinita, vengono visualizzati solo i piani consigliati per le app per la logica. Fare clic su **Visualizza tutto** per vedere tutti i piani disponibili. Al livello gratuito è possibile eseguire l'app per la logica una volta ogni ora e usare un massimo di 1.000 azioni al mese.

7. Creare un **Gruppo di risorse** per il flusso.

	I gruppi di risorse fungono da contenitori per le app. Tutte le risorse per l'app faranno parte dello stesso gruppo di risorse.

8. Se si dispone di più di una sottoscrizione di Azure, scegliere quella che si intende usare.

9. Scegliere la **Località** di esecuzione dell'app per la logica.

	![Visualizzazione Crea app per le API](./media/app-service-logic-create-a-logic-app/gallery.png)

10. Fare clic su **Create**. L'operazione di provisioning può richiedere uno o due minuti.

11. Ripetere la procedura con [Dropbox](https://portal.azure.com/#create/microsoft_com.DropboxConnector.0.2.2).

## Avviare l'app per la logica

A questo punto è necessario creare l'app per la logica.

1. Fare clic sul pulsante **+ Nuovo** nella parte inferiore sinistra dello schermo, espandere **Web e dispositivi mobili**, quindi fare clic su **App per la logica**.

 	Verrà visualizzata la schermata Crea app per la logica, in cui è possibile specificare alcune impostazioni di base iniziali.

	![Visualizzazione Crea app per la logica](./media/app-service-logic-create-a-logic-app/createlogicapp.png)

2. In **Nome** digitare un nome significativo per l'app per la logica.

3. Scegliere il **piano di servizio app** usato durante la creazione dei connettori. La località, la sottoscrizione e il gruppo di risorse vengono selezionati automaticamente.

Le impostazioni di base sono state configurate, ma non fare ancora clic su **Crea**. Il prossimo passaggio consiste nell'aggiunta di trigger e azioni al flusso di lavoro.

## Aggiungere un trigger

I trigger attivano l'esecuzione dell'app per la logica. Si aggiungerà ora un trigger di ricorrenza, che avvia il flusso di lavoro in base a una pianificazione predefinita.

1. Sempre nella schermata **Crea app per la logica**, fare clic su **Trigger e azioni**.

	Verrà visualizzata una finestra di progettazione a schermo intero che mostra il flusso e alcuni modelli da cui iniziare.
	
2. Per questa esercitazione si **inizierà da zero**. È sempre possibile usare un modello, se si ritiene che sia utile.
    
    Sul lato destro è presente un elenco di tutti i servizi che possono includere trigger.

3. Nella sezione superiore fare clic su **Ricorrenza**.

	Verrà aggiunta una casella in cui è possibile specificare le impostazioni di ricorrenza.

	![Ricorrenza](./media/app-service-logic-create-a-logic-app/recurrence.png)

4.  Scegliere la **Frequenza** e l'**Intervallo**, ad esempio una volta ogni ora e quindi fare clic sul segno di spunta verde.

A questo punto si aggiungerà un'azione al flusso.

## Aggiungere un'azione Twitter

Le azioni sono le operazioni eseguite dal flusso di lavoro. Il numero di possibili azioni è illimitato ed è possibile organizzarle in modo che le informazioni di un'azione vengono passate alla successiva.

1. Nel riquadro destro fare clic su **Twitter Connector**.

2. Dopo il caricamento, fare clic sul pulsante **Authorize**, accedere al proprio account Twitter e quindi fare clic su **Authorize app**.

	In questo modo si consente al connettore l'accesso al proprio account Twitter. Viene visualizzato un elenco di possibili operazioni fornite dal connettore Twitter.

	![Azioni](./media/app-service-logic-create-a-logic-app/actions.png)

	> [AZURE.NOTE]Il pulsante **Authorize** usa la sicurezza OAUTH per connettersi a servizi SaaS, ad esempio Twitter. Altre informazioni su OAUTH sono disponibili nell'articolo sulla [Sicurezza OAUTH](app-service-logic-oauth-security.md).

3. Fare clic su **Ricerca tweet**, quindi in **Specificare una query** digitare ad esempio `#MicrosoftAzure` e fare clic sul segno di spunta verde.

	![Ricerca di Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

Il connettore Twitter fa ora parte del flusso di lavoro.

## Aggiungere un'operazione Dropbox e creare l'app

Il passaggio finale consiste nell'aggiungere un'azione che carica un tweet in un file di Dropbox.

1. Nel riquadro destro fare clic sul **connettore Dropbox**.

2. Al termine del provisioning, fare clic su **Authorize**, accedere al proprio account Dropbox e selezionare **Allow**.

	![Autorizzare il connettore Dropbox](./media/app-service-logic-create-a-logic-app/authorize.png)

	In questo modo si consente al connettore l'accesso al proprio account Dropbox. Viene visualizzato un elenco di possibili operazioni fornite dal connettore Dropbox.

4. Fare clic su **Carica file**.

	Verranno visualizzate le impostazioni del connettore Dropbox che è necessario impostare per passare i dati dalla ricerca in Twitter a Dropbox.

	![Dropbox](./media/app-service-logic-create-a-logic-app/dropbox.png)

3. Nel campo **Percorso file** digitare `/tweet.txt`

4. Nel campo **Contenuto** fare clic sul pulsante `...` e quindi sull'opzione **Testo tweet**.

	Viene immesso il valore `@first(body('twitterconnector')).TweetText` nella casella di testo. Questo valore generato contiene le parti seguenti:

	Parte di contenuto | Descrizione
	------------------------------------------ | ------------
	 `@` | Indica che si sta immettendo una funzione anziché un valore effettivo.
	`actions('twitterconnector').outputs.body` | Ottiene i tweet restituiti dalla query del connettore Twitter.
	`first()` | L'azione di ricerca tweet restituisce un elenco, ma si desidera soltanto caricare un file
	`.TweetText` | Seleziona la proprietà del messaggio tweet.

5. Fare clic sul segno di spunta verde per salvare le impostazioni del connettore.

5. Dopo avere completato la progettazione, fare clic su **Visualizzazione codice** nella parte superiore sinistra della finestra di progettazione e notare che si tratta del codice JSON che definisce il flusso di lavoro appena creato nella finestra di progettazione. Questo codice verrà illustrato in modo più approfondito nel [prossimo argomento][Use logic app features].

6. Fare clic sul pulsante **OK** nella parte inferiore dello schermo, quindi sul pulsante **Crea**.

	Verrà creata la nuova app per la logica.

## Gestire l'app per la logica dopo la creazione

A questo punto l'app per la logica è operativa. Ogni volta che viene eseguito, il flusso di lavoro pianificato verifica la presenza di tweet con l'hashtag specificato. Quando viene trovato un tweet corrispondente, viene inserito nella cartella Dropbox. Infine, ecco come disabilitare l'applicazione e verificarne lo stato.

1. Fare clic su **Sfoglia** sul lato sinistro dello schermo e selezionare **App per la logica**.

2. Fare clic sulla nuova app per la logica appena creata per visualizzare informazioni generali e sullo stato corrente.

3. Per modificare la nuova app per la logica, fare clic su **Trigger e azioni**.

5. Per disattivare l'app, fare clic su **Disabilita** nella barra dei comandi.

In meno di 5 minuti è stato possibile configurare una semplice app per la logica eseguita nel cloud. Per altre informazioni sull'uso delle funzionalità delle app per la logica, vedere [Usare le funzionalità delle app per la logica]. Per informazioni sulle definizioni stesse dell'app per la logica, vedere l'articolo relativo alla [creazione di definizioni di app per la logica](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features]: app-service-logic-use-logic-app-features.md
[Usare le funzionalità delle app per la logica]: app-service-logic-use-logic-app-features.md

<!---HONumber=Nov15_HO1-->