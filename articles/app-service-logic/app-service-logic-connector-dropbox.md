<properties
	pageTitle="Utilizzando il connettore Dropbox nell'applicazione per la logica nel servizio App di Azure"
	description="come introdurre Dropbox connettore in un'app per la logica nel Servizio App"
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
	ms.date="08/19/2015"
	ms.author="sameerch"/>

# Dropbox Connector

Dropbox Connector consente di caricare o scaricare file dal proprio account Dropbox.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

## Trigger e azioni

Un trigger avvia una nuova istanza in base a un evento specifico, ad esempio l'arrivo di un nuovo messaggio. Un'azione è il risultato, ad esempio dopo la ricezione di un nuovo messaggio, poi caricare il file in Dropbox.

SMTP Connector può essere usato come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per il Conettore Chatter sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Eliminare il File</li><li>Ottenere il File</li><li>Caricare il File</li><li>Elenco dei File</li></ul>


## Creare un Connettore Dropbox per l'app per la logica
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Connettore Dropbox", selezionarlo e scegliere **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà:
	![][1]
	- **Località**: scegliere la località geografica in cui si vuole distribuire il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Gruppo di risorse**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Piano di servizio app** - selezionare o creare un piano di hosting Web
	- **Piano tariffario**: scegliere un piano tariffario per il connettore
	- **Nome** - assegnare un nome a Dropbox Connector  
4. Selezionare **Crea**.


## Uso di Dropbox Connector nell'app per la logica
Dopo aver creato l'app per le API, è possibile usare Dropbox Connector come azione per l'app per la logica. A tale scopo, effettuare l'operazione seguente:

1.	Nell’app per la logica, aprire **Trigger e azioni** per aprire la finestra di progettazione delle app per la logica e configurare il flusso:
	![][3]
2.	Il connettore Dropbox è elencato nella raccolta:
	![][4]
3.	Selezionare il connettore Dropbox da aggiungere automaticamente alla finestra di progettazione. Selezionare **Authorize**, immettere le credenziali e selezionare**Allow**:
	![][5]
	![][6]
	![][7]

È ora possibile usare Dropbox Connector nel flusso. È possibile usare l'azione Dropbox "Upload File" per caricare un file nel proprio account Dropbox:
![][8]
![][9]

Configurare le proprietà di input per l'azione "Upload File" nel modo indicato di seguito:

- **File Path**: specificare il percorso del file Dropbox di destinazione da caricare. Esempio: Photos/image.png
- **Content**: specificare il contenuto del file da caricare. Spesso, questo dipenderà da un passaggio precedente nell'app per la logica.
- **Content Transfer Encoding**: specificare none o base64.
- **Overwrite**: specificare "true" per sovrascrivere il file, se esiste già.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-dropbox/img1.PNG
[2]: ./media/app-service-logic-connector-dropbox/img2.PNG
[3]: ./media/app-service-logic-connector-dropbox/img3.png
[4]: ./media/app-service-logic-connector-dropbox/img4.png
[5]: ./media/app-service-logic-connector-dropbox/img5.PNG
[6]: ./media/app-service-logic-connector-dropbox/img6.PNG
[7]: ./media/app-service-logic-connector-dropbox/img7.PNG
[8]: ./media/app-service-logic-connector-dropbox/img8.PNG
[9]: ./media/app-service-logic-connector-dropbox/img9.PNG

<!---HONumber=August15_HO8-->