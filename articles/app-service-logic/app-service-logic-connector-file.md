<properties
	pageTitle="Uso del connettore File nelle app per la logica | Servizio app di Microsoft Azure"
	description="Come creare e configurare l'app per le API o il connettore File e usarlo in un'app per la logica nel servizio app di Azure"
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="rajram"/>

# Iniziare a usare il connettore File e aggiungerlo all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Connettersi a un file system per caricare, scaricare ed eseguire altre operazioni sui file in un computer host. Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati. È possibile aggiungere il connettore File al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

Il connettore File usa Gestione connessione ibrida per la connettività ibrida al file system host.

## Creazione di un connettore File per l'app per la logica ##
Per usare il connettore File, è prima di tutto necessario creare un'istanza dell'app per le API del connettore File. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO nella parte sinistra nel portale di Azure.
2.	Passare a "Marketplace > App per le API" e cercare "File Connector".
3.	Configurare il connettore File nel modo seguente: ![][1]

	- **Nome**: assegnare un nome al connettore File
	- **Impostazioni pacchetto**
		- **Root Folder**: specificare il percorso della cartella radice nel computer host. Ad esempio, D:\\FileConnectorTest
		- **Service Bus Connection String**: specificare una stringa di connessione del bus di servizio. Assicurarsi che lo spazio dei nomi del bus di servizio sia del tipo Standard e NON Basic per consentire l'uso degli inoltri del bus di servizio. L'inoltro del bus di servizio viene usato per la connessione a Gestione connessione ibrida.
	- **Piano di servizio app**: selezionare o creare un piano di servizio app
	- **Pricing tier**: scegliere un livello di prezzo per il connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Location**: scegliere l'area geografica in cui si vuole distribuire il connettore

4. Fare clic su Create. Verrà creato un nuovo connettore File.

## Configurare Gestione connessione ibrida ##
Una volta creata l'istanza dell'app per le API, individuare il relativo dashboard. Questa operazione può essere eseguita facendo clic su Esplora > App per le API > selezionare la propria app per le API del connettore File. Viene visualizzato lo strumento Gestione connessione ibrida da configurare. Per altre informazioni su come configurare e risolvere i problemi relativi a Gestione connessione ibrida, vedere l'articolo relativo all'[uso di Gestione connessione ibrida].

## Uso del connettore File nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare il connettore File come azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore File. Seguire le istruzioni per [creare una nuova app per la logica].

2.	Aprire "Trigger e azioni" all'interno dell'app per la logica appena creata per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

3.	Il connettore File viene visualizzato nella sezione relativa alle app per le API nel gruppo di risorse nella raccolta a destra.

4.	È possibile trascinare l'app per le API del connettore File nell'editor facendo clic su "File Connector". Il connettore File espone un trigger e quattro azioni: ![][5]

6.	Ciascuna azione espone determinate proprietà. L'immagine seguente mostra le proprietà del trigger e dell'azione Get File: ![][6]

7. Una volta completata la configurazione, è possibile usare il trigger e l'azione nel flusso. È possibile configurare anche altre azioni nello stesso modo.

> [AZURE.NOTE] Una volta letto dalla cartella, il trigger del file eliminerà il file.

## API REST del connettore File ##
Per usare il connettore all'esterno di un'app per la logica, è possibile sfruttare le API REST esposte dal connettore. È possibile visualizzare le definizioni delle API selezionando Esplora-> App per le API->File Connector. Fare ora clic su API Definition nella sezione di riepilogo per visualizzare tutte le API esposte dal connettore: ![][7]

Per informazioni dettagliate sulle API, vedere l'articolo relativo alla [definizione delle API del connettore File].

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare le app per la logica di Azure prima di registrarsi per ottenere un account Azure, passare a [Prova l'app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare immediatamente un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md
[definizione delle API del connettore File]: https://msdn.microsoft.com/library/dn936296.aspx
[uso di Gestione connessione ibrida]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=AcomDC_0727_2016-->