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
   ms.date="07/01/2015"
   ms.author="andalmia"/>


# SMTP Connector

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte di un flusso di lavoro.

SMTP Connector consente di connettersi a un server SMTP e di eseguire un'azione per inviare e-mail con allegati. L'azione "Send Email" di SMTP Connector consente di inviare e-mail a indirizzi specificati.

## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'*azione* è il risultato del trigger. Ad esempio, quando si aggiorna un ordine o si aggiunge un nuovo cliente, viene inviato un messaggio di posta elettronica al nuovo cliente

SMTP Connector può essere usato come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Attualmente non sono disponibili trigger per questo connettore.

Per SMTP Connector sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Nessuno | Send email


## Creare SMTP Connector
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "SMTP Connector".
3. **Creare** il connettore.
4. Immettere il nome, il piano di servizio app e altre proprietà.
5. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
	--- | --- | ---
	User Name | Sì | Immettere un nome utente in grado di connettersi al server SMTP.
	Password | Sì | Immettere il nome utente e la password.
	Server Address | Sì | Immettere il nome o l'indirizzo IP del server SMTP.
	Server Port | Sì | Immettere il numero di porta del server SMTP.
	Enable SSL | No | Immettere *true* per usare SMTP su un canale SSL/TLS sicuro.

6. Selezionare **Crea**.

## Uso di SMTP Connector nell'app per la logica
Dopo aver creato il connettore, è possibile usare SMTP Connector come azione per l'app per la logica. A tale scopo, effettuare l'operazione seguente:

1.	Creare una nuova app per la logica:

	![][2]
2.	Aprire **Trigger e azioni** per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso di lavoro:

	![][3]
3.	SMTP Connector viene elencato nella sezione relativa alle app per le API nel gruppo di risorse della raccolta a destra. Selezionarlo:

	![][4]
4.	Selezionare SMTP Connector per aggiungerlo automaticamente alla finestra di progettazione del flusso di lavoro.

È ora possibile configurare SMTP Connector per usarlo nel flusso di lavoro. Selezionare l'azione **Send Email** e configurare le proprietà di input:

	Proprietà | Descrizione
	--- | ---
	To |Inserire l'indirizzo e-mail dei destinatari. Separare più indirizzi e-mail utilizzando il punto e virgola (;). Ad esempio, inserire: *recipient1@domain.com;recipient2@domain.com*.
	Cc | Inserire l'indirizzo e-mail dei destinatari in copia. Separare più indirizzi e-mail utilizzando il punto e virgola (;). Ad esempio, inserire: *recipient1@domain.com;recipient2@domain.com*.
	Subject | Inserire l'oggetto dell'e-mail.
	Body | Inserire il testo dell'e-mail.
	Is HTML | Quando questa proprietà è impostata su true, i contenuti del testo dell'e-mail vengono inviati in formato HTML.
	Bcc | Inserire l'indirizzo e-mail dei destinatari in copia nascosta. Separare più indirizzi e-mail utilizzando il punto e virgola (;). Ad esempio, inserire: *recipient1@domain.com;recipient2@domain.com*.
	Importance | Selezionare la priorità dell'e-mail. Le opzioni sono Normale, Bassa e Alta.
	Attachments | Allegati da inviare insieme all'e-mail. Include i seguenti campi: <ul><li>Content (String)</li><li>Content transfer Encoding (Enum) (“none”|”base64”)</li><li>Content Type (String)</li><li>Content ID (String)</li><li>File Name (String)</li></ul>

	![][5]
	![][6]

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

	<!----Image references-->
[1]: ./media/app-service-logic-connector-smtp/img1.PNG
[2]: ./media/app-service-logic-connector-smtp/img2.PNG
[3]: ./media/app-service-logic-connector-smtp/img3.png
[4]: ./media/app-service-logic-connector-smtp/img4.PNG
[5]: ./media/app-service-logic-connector-smtp/img5.PNG
[6]: ./media/app-service-logic-connector-smtp/img6.PNG

<!---HONumber=July15_HO5-->