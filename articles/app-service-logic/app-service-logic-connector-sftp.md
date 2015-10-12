<properties
	pageTitle="Uso del connettore SFTP nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore SFTP e usarlo in un'app per la logica in Azure App Service"
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
	ms.date="08/23/2015"
	ms.author="sameerch"/>

# Uso del connettore SFTP e aggiunta all'app per la logica
Usare il connettore SFTP per spostare dati da e verso un server SFTP. È possibile scaricare, caricare ed elencare file da e verso un server SFTP.

Le app per la logica possono essere attivate in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. È possibile aggiungere il connettore SFTP al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Creare il connettore SFTP per l'app per la logica ##
È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "SFTP Connector", selezionarlo e fare clic su **Crea**.
3. Configurare il connettore SFTP nel modo seguente:  
![][1]
	- **Location**: scegliere l'area geografica in cui si vuole distribuire il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Web hosting plan**: selezionare o creare un piano di hosting Web
	- **Pricing tier**: scegliere un livello di prezzo per il connettore
	- **Name**: assegnare un nome al connettore SFTP
	- **Impostazioni pacchetto**
		- **Server Address**: specificare il nome o l'indirizzo IP del server SFTP
		- **Accept Any SSH Server HostKey**: determina se un ID digitale della chiave dell'host SSH pubblica deve essere accettato. Se impostato su False, la chiave dell'host verrà confrontata con la chiave specificata nella proprietà “SSH Server Host Key Finger Print”.
		- **SSH Server HostKey**: specificare l'ID digitale della chiave host pubblica per il server SSH - *facoltativo*.
		- **Root Folder**: specificare il percorso della cartella radice. Se il campo è vuoto, per impostazione predefinita viene impostata la radice.
		- **Encrypt Cipher**: specificare l'algoritmo di crittografia - *facoltativo*.
		- **Server Port**: specificare il numero di porta del server SFTP.
4. Fare clic su Create. Viene creato un nuovo connettore SFTP.

5. Individuare l'app per le API appena creata scegliendo Sfoglia -> App per le API -> <Name of the API App just created>. È possibile notare che il componente "Security" non è configurato:  
![][2]
6. Fare clic sul componente "Security" per configurarlo per il connettore SFTP. A tale scopo, indicare il nome utente, la password, la chiave privata e la password del file PPK. Nell'area Security selezionare la scheda di autorizzazione "Password", "Privatekey" o "MutliFactor" e fornire le proprietà richieste:  
![][3]  
![][4]  
![][5]  
6. Una volta salvata la configurazione, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare il connettore SFTP.

## Usare il connettore SFTP nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare il connettore SFTP come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse del connettore SFTP:  
![][6]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso:  
![][7]
3.	Il connettore SFTP viene visualizzato nella sezione relativa alle app per le API nel gruppo di risorse nella raccolta a destra:  
![][8]
4.	È possibile trascinare l'app per le API del connettore SFTP nell'editor facendo clic su "SFTP Connector".

5.	È ora possibile usare il connettore SFTP nel flusso. È possibile usare il file recuperato dal trigger SFTP ("TriggerOnFileAvailable") in altre azioni del flusso.

	> [AZURE.IMPORTANT]Dopo aver elaborato il file recuperato, il trigger SFTP "TriggerOnFileAvailable" lo elimina.

6.	Configurare le proprietà di input per il trigger SFTP nel modo indicato di seguito:

	- **Folder Path**: specificare il percorso della cartella da cui devono essere recuperati i file.
	- **The type of the file: text or binary**: selezionare il tipo di file.
	- **File Mask**: specificare il filtro file da applicare per recuperare i file. '*' recupera tutti i file contenuti nella cartella specificata.
	- **Exclude File Mask**: specificare il filtro file da applicare per escludere i file. Se è impostata anche la proprietà "File Mask", Exclude File Mask verrà applicata per prima.


	![][9]  
	![][10]

7.	È possibile usare le azioni SFTP nel flusso in modo analogo. È possibile usare l'azione "Upload File" per caricare un file nel server SFTP. Configurare le proprietà di input per l'azione "Upload File" nel modo indicato di seguito:

	- **Content**: specificare il contenuto del file da caricare.
	- **Content Transfer Encoding**: specificare none o base64.
	- **File Path**: specificare il percorso del file da caricare.
	- **Overwrite**: specificare "true" per sovrascrivere il file, se esiste già.
	- ****Append If Exists**: specificare "true" o "false". Se il parametro è impostato su "true", i dati vengono aggiunti al file, se esistente. Se impostato su "false", il file, se esistente, viene sovrascritto.
	- **Temporary Folder**: se specificato, l'adattatore caricherà il file in 'Temporary Folder Path'. Al termine del caricamento, il file verrà spostato in 'Folder Path'. Per garantire che l'operazione di spostamento sia atomica, Temporary Folder Path deve trovarsi nello stesso disco fisico di Folder Path. È possibile usare Temporary Folder solo quando la proprietà Aggiungi se esiste è disabilitata.

	![][11]  
	![][12]

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-sftp/img1.PNG
[2]: ./media/app-service-logic-connector-sftp/img2.PNG
[3]: ./media/app-service-logic-connector-sftp/img3.PNG
[4]: ./media/app-service-logic-connector-sftp/img4.PNG
[5]: ./media/app-service-logic-connector-sftp/img5.PNG
[6]: ./media/app-service-logic-connector-sftp/img6.PNG
[7]: ./media/app-service-logic-connector-sftp/img7.png
[8]: ./media/app-service-logic-connector-sftp/img8.png
[9]: ./media/app-service-logic-connector-sftp/img9.PNG
[10]: ./media/app-service-logic-connector-sftp/img10.PNG
[11]: ./media/app-service-logic-connector-sftp/img11.PNG
[12]: ./media/app-service-logic-connector-sftp/img12.PNG

<!---HONumber=Oct15_HO1-->