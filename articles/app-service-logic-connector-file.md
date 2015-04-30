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
	ms.date="04/09/2015"
	ms.author="andalmia"/>

# Uso di File Connector nell'app per la logica #

Le app per la logica possono eseguire un'attivazione in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. 

File Connector consente di caricare, scaricare ed eliminare file dal file system su un computer host.

## Creare File Connector per l'app per la logica ##
Per usare File Connector, è necessario innanzitutto creare un'istanza dell'app per le API File Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "File Connector".
3.	Configurare File Connector nel modo seguente:
 
	![][1]
 
	- **Name**: assegnare un nome a File Connector
	- **Impostazioni pacchetto**
		- **Root Folder**: specificare il percorso della cartella radice nel computer host, ed esempio D:\FileConnectorTest
		- **Service Bus Connection String**: fornire una stringa di connessione del bus di servizio. Assicurarsi che lo spazio dei nomi del bus di servizio sia di tipo Standard, NON Base
	- **App Service plan**: selezionare o creare un piano di servizio app
	- **Pricing tier**: scegliere un piano tariffario per il connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Location**: scegliere la località geografica in cui si vuole distribuire il connettore

4. Fare clic su Create. Verrà creato un nuovo File Connector.
5. Una volta creata l'istanza dell'app per le API, individuare il relativo dashboard. Questa operazione può essere eseguita facendo clic su Browse->Api Apps->Select your File Connector API App
6. Nella sezione "Hybrid Connection" del dashboard di File Connector viene visualizzata la dicitura "On-Premise Setup Incomplete". Per completare la configurazione ibrida, aprire il dashboard dal computer host con il file system a cui è necessario connettersi. Fare clic su "Hybrid Connection". Nel pannello "Hybrid Connection" aperto fare clic sul collegamento "Download and configure" per installare On-premises Hybrid Connection Manager

	![][2]

7. Verrà avviato un programma di installazione che richiederà di immettere un valore per Relay Listen Connection String. Copiare e incollare la "Primary Configuration String" dal pannello "Hybrid Connection". Fare clic su Install.

	![][3]

8. Per verificare che l'installazione ibrida abbia avuto esito positivo, riaprire il dashboard di File Connector. Lo stato di Hybrid Connection deve essere "Connected"

	![][4]

## Usare File Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare File Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di File Connector. Seguire le istruzioni per [creare una nuova app per la logica].  	
	
2.	Aprire "Triggers and Actions" all'interno dell'app per la logica appena creata per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.  	
	
3.	File Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.
 	
4.	È possibile trascinare l'app per le API File Connector nell'editor facendo clic su "File Connector". File Connector espone un trigger e quattro azioni.
 
	![][5]

6.	Ciascuna azione espone determinate proprietà. L'immagine seguente mostra le proprietà del trigger e dell'azione Get File
 
	![][6]

7. Una volta completata la configurazione, è possibile usare il trigger e l'azione nel flusso. È possibile configurare anche altre azioni nello stesso modo.

8. Per usare il connettore all'esterno di un'app per la logica, è possibile sfruttare le API REST esposte dal connettore. È possibile visualizzare le definizioni delle API selezionando Browse->Api App->File Connector. È ora possibile fare clic su API Definition nella sezione di riepilogo per visualizzare tutte le API esposte dal connettore.

	![][7]

9. Per dettagli sulle API, vedere l'articolo relativo alla [definizione delle API di File Connector].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[2]: ./media/app-service-logic-connector-file/img2.PNG
[3]: ./media/app-service-logic-connector-file/img3.PNG
[4]: ./media/app-service-logic-connector-file/img4.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md
[Definizione delle API di File Connector]: https://msdn.microsoft.com/en-US/library/dn936296.aspx

<!--HONumber=52-->