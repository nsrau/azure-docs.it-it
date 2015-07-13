<properties
	pageTitle="OneDrive Connector"
	description="Introduzione a OneDrive Connector"
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

# Uso di OneDrive Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

OneDrive Connector consente di caricare, scaricare ed eliminare file dall'account OneDrive.

## Creare OneDrive Connector per l'app per la logica ##
Per usare OneDrive Connector, è necessario innanzitutto creare un'istanza dell'app per le API OneDrive Connector. È possibile eseguire questa operazione direttamente dall'area di progettazione dell'app per la logica oppure dall'esterno. Per creare un'istanza all'esterno dell'area di progettazione, seguire questa procedura:

1.	Aprire Azure Marketplace dalla home page del portale di Azure.
2.	In "Tutto" cercare "OneDrive Connector".
3.	Configurare OneDrive Connector nel modo seguente:

	![][1] - **Nome**: assegnare un nome al connettore - **Piano di servizio app**: selezionare o creare un piano di servizio app - **Piano tariffario**: scegliere un piano tariffario per il connettore - **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui deve risiedere il connettore - **Sottoscrizione**: scegliere una sottoscrizione in cui creare questo connettore - **Località**: scegliere la località geografica in cui si vuole distribuire il connettore

4. Fare clic su Crea. Verrà creato un nuovo OneDrive Connector.
5. Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare OneDrive Connector.

## Uso di OneDrive Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare OneDrive Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di OneDrive Connector. Seguire le istruzioni per [creare una nuova app per la logica].

2.	Aprire "Trigger e azioni" all'interno dell'app per la logica appena creata per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

3.	OneDrive Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.

	![][2]
4.	È possibile trascinare l'app per le API OneDrive Connector nell'editor facendo clic su "OneDrive Connector". Fare clic sul pulsante Autorizza. Fornire le credenziali di Microsoft (se non è stata eseguita la connessione automatica). Fare clic su "Sì" per consentire l'accesso.

	![][3] ![][4]

5.	È ora possibile usare OneDrive Connector nel flusso. Attualmente in OneDrive Connector non sono disponibili trigger. Le azioni disponibili sono: Get File, Upload File, Delete File e List Files.

	![][5]

6.	Di seguito viene descritto l'uso dell'azione "Upload File". L'azione "Upload File" di OneDrive consente di caricare un file nell'account OneDrive.

	![][6]

	Configurare le proprietà di input per l'azione "Upload File" nel modo indicato di seguito:

 - **File Path**: specificare il percorso del file da caricare.
 - **Content**: specificare il contenuto del file da caricare.
 - **Content Transfer Encoding**: specificare none o base64.
 - **Overwrite**: specificare "true" per sovrascrivere il file, se esiste già. Si tratta di una proprietà avanzata.

7. Una volta fornite le informazioni richieste, l'azione "Upload File" è configurata e può essere usata nel flusso. È possibile configurare anche altre azioni nello stesso modo.

8. Per usare il connettore all'esterno di un'app per la logica, è possibile sfruttare le API REST esposte dal connettore. È possibile visualizzare le definizioni delle API selezionando Browse->Api App->OneDrive Connector. È ora possibile fare clic su Definizione API Definition nella sezione di riepilogo per visualizzare tutte le API esposte dal connettore.

	![][7]

9. Per informazioni dettagliate sulle API, vedere l'articolo relativo alla [definizione dell'API OneDrive].

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
[definizione dell'API OneDrive]: https://msdn.microsoft.com/it-it/library/dn974227.aspx

<!---HONumber=62-->