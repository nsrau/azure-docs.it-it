<properties
	pageTitle="File Connector"
	description="Introduzione a File Connector"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="andalmia"/>

# Uso di File Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

File Connector consente di caricare, scaricare ed eliminare file dal file system su un computer host. Usa Gestione connessione ibrida per la connettività ibrida al computer host.

## Creare File Connector per l'app per la logica ##
Per usare File Connector, è necessario innanzitutto creare un'istanza dell'app per le API File Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO nella parte sinistra nel portale di Azure.
2.	Passare a "Web e dispositivi mobili > App per le API" e cercare "File Connector".
3.	Configurare File Connector nel modo seguente:

	![][1]

	- **Nome**: assegnare un nome al connettore File
	- **Impostazioni pacchetto**
		- **Cartella radice**: specificare il percorso della cartella radice nel computer host. Ad esempio, D:\\FileConnectorTest
		- **Stringa di connessione del bus di servizio**: specificare una stringa di connessione del bus di servizio. Assicurarsi che lo spazio dei nomi del bus di servizio sia del tipo Standard e NON Basic per consentire l'uso degli inoltri del bus di servizio. L'inoltro del bus di servizio viene usato per la connessione a Gestione connessione ibrida.
	- **Piano di servizio app**: selezionare o creare un piano di servizio app
	- **Piano tariffario**: scegliere un piano tariffario per il connettore
	- **Gruppo di risorse**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Località**: scegliere la località geografica in cui si vuole distribuire il connettore

4. Fare clic su Crea. Verrà creato un nuovo File Connector.

## Configurare Gestione connessione ibrida ##
Una volta creata l'istanza dell'app per le API, individuare il relativo dashboard. Questa operazione può essere eseguita facendo clic su Sfoglia > App per le API > selezionare la propria app per le API connettore File. Verrà visualizzato lo strumento Gestione connessione ibrida da configurare. Per altre informazioni su come configurare e risolvere i problemi relativi a Gestione connessione ibrida, vedere l'articolo relativo all'[uso di Gestione connessione ibrida].

## Uso di File Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare File Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di File Connector. Seguire le istruzioni per [creare una nuova app per la logica].

2.	Aprire "Trigger e azioni" all'interno dell'app per la logica appena creata per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

3.	File Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.

4.	È possibile trascinare l'app per le API File Connector nell'editor facendo clic su “File Connector”. File Connector espone un trigger e quattro azioni.

	![][5]

6.	Ciascuna azione espone determinate proprietà. L'immagine seguente mostra le proprietà del trigger e dell'azione Get File

	![][6]

7. Una volta completata la configurazione, è possibile usare il trigger e l'azione nel flusso. È possibile configurare anche altre azioni nello stesso modo.

> [AZURE.NOTE]Una volta letto dalla cartella, il trigger del file eliminerà il file.

## API REST del connettore File ##
Per usare il connettore all'esterno di un'app per la logica, è possibile sfruttare le API REST esposte dal connettore. È possibile visualizzare le definizioni delle API selezionando Browse->Api App->File Connector. È ora possibile fare clic su Definizione API Definition nella sezione di riepilogo per visualizzare tutte le API esposte dal connettore.

  ![][7]

Per informazioni dettagliate sulle API, vedere l'articolo relativo alla [definizione delle API di File Connector].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md
[definizione delle API di File Connector]: https://msdn.microsoft.com/en-US/library/dn936296.aspx
[uso di Gestione connessione ibrida]: app-service-logic-hybrid-connection-manager.md

<!---HONumber=July15_HO3-->