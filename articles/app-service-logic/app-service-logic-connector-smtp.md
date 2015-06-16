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

Le app per la logica possono eseguire un'attivazione in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. 

SMTP Connector consente di connettersi a un server SMTP e di eseguire un'azione per inviare e-mail con allegati.

- L'azione "Send Email" di SMTP Connector consente di inviare e-mail a indirizzi specificati. 

## Creare SMTP Connector per l'app per la logica ##
Per usare SMTP Connector, è necessario innanzitutto creare un'istanza dell'app per le API SMTP Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "SMTP Connector".
3.	Configurare SMTP Connector nel modo seguente:
 
	![][1]
	- **Location**: scegliere la località geografica in cui si vuole distribuire il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Web hosting plan**: selezionare o creare un piano di hosting Web
	- **Pricing tier**: scegliere un piano tariffario per il connettore
	- **Name**: assegnare un nome a SMTP Connector
	- **Impostazioni pacchetto**
		- **User Name**: specificare il nome utente per la connessione al server SMTP
		- **Password**: specificare la password per la connessione al server SMTP
		- **Server Address**: specificare il nome o l'indirizzo IP del server SMTP
		- **Server Port**: specificare il numero di porta del server SMTP
		- **Enable SSL**: specificare true per usare SMTP su un canale SSL/TLS sicuro
4.	Fare clic su Create. Verrà creato un nuovo SMTP Connector.
5.	Una volta creata l'istanza dell'app per le API, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare SMTP Connector. 

## Usare SMTP Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare SMTP Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di SMTP Connector.
 
	![][2]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. 
 
	![][3]
3.	SMTP Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra. Selezionarlo.
 
	![][4]
4.	È possibile trascinare l'app per le API SMTP Connector nell'editor facendo clic su "SMTP Connector". 
	
7.	È ora possibile usare SMTP Connector nel flusso. Selezionare l'azione "Send Email" e configurare le proprietà di input come segue:
	- **To** - Indirizzo e-mail del destinatario. Separare più indirizzi e-mail con un punto e virgola (;). Ad esempio: recipient1@domain.com;recipient2@domain.com.
	- **Cc** - Indirizzo e-mail dei destinatari della copia per conoscenza. Separare più indirizzi e-mail con un punto e virgola (;). Ad esempio: recipient1@domain.com;recipient2@domain.com.
	- **Subject** - Oggetto del messaggio e-mail.
	- **Body** - Corpo del messaggio e-mail.
	- **Is HTML** - Quando questa proprietà è impostata su true, il contenuto del corpo viene inviato in formato HTML.
	- **Bcc** - Indirizzo e-mail dei destinatari in copia per conoscenza nascosta. Separare più indirizzi e-mail con un punto e virgola (;). Ad esempio: recipient1@domain.com;recipient2@domain.com.
	- **Importance** - Importanza del messaggio e-mail. Le opzioni sono Normal, Low e High.
	- **Attachments** - Allegati da inviare insieme al messaggio e-mail. Un allegato contiene i seguenti campi:
		- Content (stringa)
		- Content transfer Encoding (enum) ("None"|"Base64")
		- Content Type (stringa)
		- Content ID (stringa)
		- File Name (stringa)
	 
	
![][5] 
![][6]


<!--Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG


<!--HONumber=52--> 