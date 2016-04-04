<properties
	pageTitle="Uso del connettore OneDrive nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore OneDrive e usarlo in un'app per la logica in Azure App Service"
	authors="rajeshramabathiran"
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
	ms.date="03/16/2016"
	ms.author="rajram"/>

# Uso del connettore OneDrive e aggiunta all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica. Per la versione dello schema 2015-08-01-preview, fare clic su [API OneDrive](../connectors/connectors-create-api-onedrive.md).

Connettersi a OneDrive per caricare, scaricare ed eliminare i file. Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore OneDrive al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Creare un connettore OneDrive per l'app per la logica ##
Per usare il connettore OneDrive, è innanzitutto necessario creare un'istanza dell'app per le API del connettore OneDrive. È possibile eseguire questa operazione direttamente dall'area di progettazione dell'app per la logica oppure dall'esterno. Per creare un'istanza all'esterno dell'area di progettazione, seguire questa procedura:

1.	Aprire Azure Marketplace dalla home page del portale di Azure.
2.	In "Tutto" cercare "OneDrive Connector".
3.	Configurare il connettore OneDrive nel modo seguente:

	![][1]
	- **Nome**: assegnare un nome a OneDrive Connector
	- **Piano di servizio app**: selezionare o creare un piano di servizio app
	- **Pricing tier**: scegliere un livello di prezzo per il connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Location**: scegliere l'area geografica in cui si vuole distribuire il connettore

4. Fare clic su Create. Verrà creato un nuovo connettore OneDrive.
5. Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore OneDrive.

## Usare il connettore OneDrive nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare il connettore OneDrive come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore OneDrive. Seguire le istruzioni per [creare una nuova app per la logica].

2.	Aprire "Triggers and Actions" all'interno dell'app per la logica appena creata per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

3.	Il connettore OneDrive viene visualizzato nella sezione relativa alle app per le API nel gruppo di risorse nella raccolta a destra.

	![][2]
4.	È possibile trascinare l'app per le API del connettore OneDrive nell'editor facendo clic su "OneDrive Connector". Fare clic sul pulsante Authorize. Fornire le credenziali di Microsoft (se non è stata eseguita la connessione automatica). Fare clic su "Sì" per consentire l'accesso.

	![][3]
	![][4]

5.	È ora possibile usare il connettore OneDrive nel flusso. Attualmente nel connettore OneDrive non sono disponibili trigger. Le azioni disponibili sono: Get File, Upload File, Delete File e List Files.

	![][5]

6.	Di seguito viene descritto l'uso dell'azione "Upload File". L'azione "Upload File" di OneDrive consente di caricare un file nell'account OneDrive.

	![][6]

	Configurare le proprietà di input per l'azione "Upload File" nel modo indicato di seguito:

 - **File Path**: specificare il percorso del file da caricare.
 - **Content**: specificare il contenuto del file da caricare.
 - **Content Transfer Encoding**: specificare none o base64.
 - **Overwrite**: specificare "true" per sovrascrivere il file, se esiste già. Si tratta di una proprietà avanzata.

7. Una volta fornite le informazioni richieste, l'azione "Upload File" è configurata e può essere usata nel flusso. È possibile configurare anche altre azioni nello stesso modo.

8. Per usare il connettore all'esterno di un'app per la logica, è possibile sfruttare le API REST esposte dal connettore. È possibile visualizzare le definizioni delle API selezionando Browse->Api App->OneDrive Connector. È ora possibile fare clic su API Definition nella sezione di riepilogo per visualizzare tutte le API esposte dal connettore.

	![][7]

9. Per informazioni dettagliate sulle API, vedere l'articolo relativo alla [definizione dell'API OneDrive].

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-onedrive/img1.PNG
[2]: ./media/app-service-logic-connector-onedrive/img2.PNG
[3]: ./media/app-service-logic-connector-onedrive/img3.PNG
[4]: ./media/app-service-logic-connector-onedrive/img4.PNG
[5]: ./media/app-service-logic-connector-onedrive/img5.PNG
[6]: ./media/app-service-logic-connector-onedrive/img6.PNG
[7]: ./media/app-service-logic-connector-onedrive/img7.PNG

<!-- Links -->
[creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md
[definizione dell'API OneDrive]: https://msdn.microsoft.com/library/dn974227.aspx

<!---HONumber=AcomDC_0323_2016-->