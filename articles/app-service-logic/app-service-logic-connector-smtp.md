<properties 
   pageTitle="App per le API SMTP Connector" 
   description="Come usare SMTP Connector" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/31/2015"
   ms.author="adgoda"/>


# Uso di SMTP Connector nell'app per la logica #

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso.

SMTP Connector consente di connettersi a un server SMTP e di eseguire un'azione per inviare e-mail con allegati. L'azione "Send Email" di SMTP Connector consente di inviare e-mail a indirizzi specificati.

## Creare SMTP Connector per l'app per la logica ##
Per usare SMTP Connector, è necessario innanzitutto creare un'istanza dell'app per le API SMTP Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Passare a "Web e dispositivi mobili > Azure Marketplace" e cercare "SMTP Connector".
3.	Configurare SMTP Connector nel modo seguente:
 
	![][1] - **Località**: scegliere la località geografica in cui verrà distribuito il connettore - **Sottoscrizione**: scegliere la sottoscrizione in cui verrà creato il connettore - **Gruppo di risorse**: selezionare o creare il gruppo di risorse in cui risiederà il connettore - **Piano di hosting Web**: selezionare o creare un piano di hosting Web - **Piano tariffario**: scegliere il piano tariffario per il connettore - **Nome**: assegnare un nome al connettore SMTP - **Impostazioni pacchetto** - **Nome utente**: specificare il nome utente per la connessione al server SMTP - **Password**: specificare la password per la connessione al server SMTP - **Indirizzo server**: specificare il nome del server o l'indirizzo IP del server SMTP - **Porta del server**: specificare il numero di porta del server SMTP - **Abilita SSL**: specificare true per usare il protocollo SMTP tramite un canale SSL/TLS sicuro
4.	Fare clic su Crea. Verrà creato un nuovo SMTP Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare SMTP Connector. 

## Uso di SMTP Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare SMTP Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di SMTP Connector.
 
	![][2]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. 
 
	![][3]
3.	Il connettore SMTP verrà visualizzato nella sezione "App per le API in questo gruppo di risorse" della raccolta a destra. Selezionarlo.
 
	![][4]
4.	È possibile trascinare l'app per le API SMTP Connector nell'editor facendo clic su “SMTP Connector”. 
	
7.	È ora possibile usare SMTP Connector nel flusso. Selezionare l'azione "Send Email" e configurare le proprietà di input come segue:
	- **To**: indirizzo di posta elettronica dei destinatari. Separare più indirizzi e-mail con un punto e virgola (;). Ad esempio: recipient1@domain.com;recipient2@domain.com.
	- **Cc**: indirizzo di posta elettronica dei destinatari della copia per conoscenza. Separare più indirizzi e-mail con un punto e virgola (;). Ad esempio: recipient1@domain.com;recipient2@domain.com.
	- **Subject**: oggetto del messaggio di posta elettronica.
	- **Body**: corpo del messaggio di posta elettronica.
	- **Is HTML**: quando questa proprietà è impostata su true, il contenuto del corpo viene inviato in formato HTML.
	- **Bcc**: indirizzo di posta elettronica dei destinatari in copia per conoscenza nascosta. Separare più indirizzi e-mail con un punto e virgola (;). Ad esempio: recipient1@domain.com;recipient2@domain.com.
	- **Importance**: importanza del messaggio di posta elettronica. Le opzioni sono Normal, Low, High.
	- **Attachments**: allegati da inviare insieme al messaggio di posta elettronica. Un allegato contiene i seguenti campi:
		- Content (stringa)
		- Content transfer Encoding (Enum) ("none"|"base64")
		- Content Type (stringa)
		- Content ID (stringa)
		- File Name (stringa)
	 
	
	![][5] ![][6]


	<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG
 

<!---HONumber=62-->