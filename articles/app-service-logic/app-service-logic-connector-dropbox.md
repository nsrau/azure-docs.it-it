<properties
	pageTitle="Uso del connettore Dropbox nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore Dropbox e usarlo in un'app per la logica in Azure App Service"
	authors="anuragdalmia"
	manager="erikre"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="sameerch"/>

# Uso del connettore Dropbox e aggiunta all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica. Per la versione dello schema 2015-08-01-preview, fare clic su [API Dropbox](../connectors/create-api-dropbox.md).

Connettersi all'account Dropbox per caricare o scaricare i file. Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati. È possibile aggiungere il connettore Dropbox al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Trigger e azioni

Un trigger avvia una nuova istanza in base a un evento specifico, ad esempio l'arrivo di un nuovo messaggio. Un'azione è il risultato, ad esempio dopo la ricezione di un nuovo messaggio, poi caricare il file in Dropbox.

Il connettore Dropbox può essere usato come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per il connettore Dropbox sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Eliminare il File</li><li>Ottenere il File</li><li>Caricare il File</li><li>Elenco dei File</li></ul>


## Creare il connettore Dropbox per l'app per la logica
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "Dropbox Connector", selezionarlo e fare clic su **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà: 
	![][1]
	- **Location**: scegliere l'area geografica in cui si vuole distribuire il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **App Service plan**: selezionare o creare un piano di hosting Web
	- **Pricing tier**: scegliere un livello di prezzo per il connettore
	- **Nome** - assegnare un nome a Dropbox Connector  
4. Selezionare **Create**.


## Usare il connettore Dropbox nell'app per la logica
Dopo aver creato l'app per le API, è possibile usare il connettore Dropbox come azione per l'app per la logica. A tale scopo, seguire questa procedura:

1.	Nell'app per la logica, aprire **Triggers and Actions** per aprire la finestra di progettazione delle app per la logica e configurare il flusso: 
	![][3]
2.	Il connettore Dropbox è elencato nella raccolta:  
	![][4]
3.	Selezionare il connettore Dropbox per aggiungerlo automaticamente alla finestra di progettazione. Selezionare **Authorize**, immettere le credenziali e selezionare **Allow**:  
	![][5] 
	![][6] 
	![][7]

È ora possibile usare il connettore Dropbox nel flusso. È possibile usare l'azione Dropbox "Upload File" per caricare un file nel proprio account Dropbox: 
	![][8] 
	![][9]

Configurare le proprietà di input per l'azione "Upload File" nel modo indicato di seguito:

- **File Path**: specificare il percorso del file Dropbox di destinazione da caricare. Esempio: Photos/image.png
- **Content**: specificare il contenuto del file da caricare. Spesso, questo dipenderà da un passaggio precedente nell'app per la logica.
- **Content Transfer Encoding**: specificare none o base64.
- **Overwrite**: specificare "true" per sovrascrivere il file, se esiste già.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

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

<!---HONumber=AcomDC_0224_2016-->