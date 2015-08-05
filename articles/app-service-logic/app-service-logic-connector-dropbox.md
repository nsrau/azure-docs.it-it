<properties
	pageTitle="Dropbox Connector"
	description="Introduzione a Dropbox Connector"
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
	ms.author="sameerch"/>

# Uso di Dropbox Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

Dropbox Connector consente di caricare o scaricare file dal proprio account Dropbox.

## Creare Dropbox Connector per l'app per la logica ##
Per usare Dropbox Connector, è necessario innanzitutto creare un'istanza dell'app per le API Dropbox Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Passare a "Web e dispositivi mobili > Azure Marketplace" e cercare "Dropbox Connector".
3.	Configurare Dropbox Connector nel modo seguente:
 
	![][1]
	- **Località**: scegliere la località geografica in cui verrà distribuito il connettore
	- **Sottoscrizione**: scegliere la sottoscrizione in cui verrà creato il connettore
	- **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui deve risiedere il connettore
	- **Piano di servizio app**: selezionare o creare un piano di hosting Web
	- **Piano tariffario**: scegliere il piano tariffario per il connettore
	- **Nome**: assegnare un nome al connettore.
4. Fare clic su Crea. Verrà creato un nuovo Dropbox Connector.
5. Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare Dropbox Connector.

## Uso di Dropbox Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare Dropbox Connector come azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di Dropbox Connector.

	![][2]
2.	Aprire "Trigger e azioni" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.

	![][3]
3.	Dropbox Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.

	![][4]
4.	È possibile trascinare l'app per le API Dropbox Connector nell'editor facendo clic su “Dropbox Connector”. Fare clic sul pulsante Autorizza. Fornire le credenziali Dropbox. Fare clic su "Consenti".

	![][5]
	![][6]
	![][7]
6.	È ora possibile usare Dropbox Connector nel flusso. È possibile usare l'azione Dropbox "Upload File" per caricare un file nel proprio account Dropbox.

	![][8]
	![][9]

Configurare le proprietà di input per l'azione "Upload File" nel modo indicato di seguito:

- **File Path**: specificare il percorso del file Dropbox di destinazione da caricare. Esempio: Photos/image.png
- **Content**: specificare il contenuto del file da caricare. Spesso, questo dipenderà da un passaggio precedente nell'app per la logica.
- **Content Transfer Encoding**: specificare none o base64.
- **Overwrite**: specificare "true" per sovrascrivere il file, se esiste già.


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

<!---HONumber=July15_HO4-->