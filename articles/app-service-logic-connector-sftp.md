<properties 
	pageTitle="SFTP Connector"
	description="Introduzione a SFTP Connector"
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
	ms.date="03/31/2015"
	ms.author="adgoda"/>

# Uso di SFTP Connector nell'app per la logica #

Le app per la logica possono eseguire un'attivazione in base a diverse origini dati e offrono connettori per ottenere ed elaborare i dati come parte del flusso. 

SFTP Connector consente di spostare dati da e verso un server SFTP. Permette di scaricare, caricare e elencare file da e verso un server SFTP.

## Creare SFTP Connector per l'app per la logica ##
Per usare SFTP Connector, è necessario innanzitutto creare un'istanza dell'app per le API SFTP Connector. Attenersi alla procedura riportata di seguito:

1.	Aprire Azure Marketplace con l'opzione + NUOVO in basso a sinistra nel portale di Azure.
2.	Andare a "Web e dispositivi mobili > App per le API" e cercare "SFTP Connector".
3.	Configurare SFTP Connector nel modo seguente:
 
	![][1] 
	- **Location**: scegliere la località geografica in cui si vuole distribuire il connettore
	- **Subscription**: scegliere una sottoscrizione in cui si vuole creare questo connettore
	- **Resource group**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
	- **Web hosting plan**: selezionare o creare un piano di hosting Web
	- **Pricing tier**: scegliere un piano tariffario per il connettore
	- **Name**: assegnare un nome a SFTP Connector
	- **Impostazioni pacchetto** 
		- **Server Address**: specificare il nome o l'indirizzo IP del server SFTP
		- **Accept Any SSH Server HostKey**: determina se un ID digitale della chiave dell'host SSH pubblica deve essere accettato. Se impostato su False, la chiave dell'host verrà confrontata con la chiave specificata nella proprietà "SSH Server Host Key Finger Print".
		- **SSH Server HostKey**: specificare l'ID digitale della chiave host pubblica per il server SSH.
		- **Root Folder**: specificare il percorso della cartella radice.
		- **Encrypt Cipher**: specificare l'algoritmo di crittografia.
		- **Server Port**: specificare il numero di porta del server FTP.
4. Fare clic su Create. Verrà creato un nuovo SFTP Connector.

5. Individuare l'app per le API appena creata scegliendo Sfoglia -> App per le API -> <Nome dell'app per le API appena creata>. È possibile notare che il componente "Security" non è configurato. 

	![][2]
6. Fare clic sul componente "Security" per configurarlo per SFTP Connector. A tale scopo, indicare il nome utente, la password, la chiave privata e la password del file PPK. 
Nell'area Security selezionare la scheda di autorizzazione "Password", "Privatekey" o "MutliFactor" e fornire le proprietà richieste.

	![][3]
	![][4]
	![][5]
6. Una volta salvata la configurazione, è possibile creare un'app per la logica nello stesso gruppo di risorse per usare SFTP Connector. 

## Usare SFTP Connector nell'app per la logica ##
Dopo aver creato l'app per le API, è possibile usare SFTP Connector come trigger o azione per l'app per la logica. A questo scopo, è necessario:

1.	Creare una nuova app per la logica e scegliere lo stesso gruppo di risorse di SFTP Connector.
 	
	![][6]
2.	Aprire "Triggers and Actions" per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso. 
 	
	![][7]
3.	SFTP Connector verrà visualizzato nella sezione "API Apps in this resource group" della raccolta a destra.
 
	![][8]
4.	È possibile trascinare l'app per le API SFTP Connector nell'editor facendo clic su "SFTP Connector"
 
	
6.	È ora possibile usare SFTP Connector nel flusso. È possibile usare il file recuperato dal trigger SFTP ("TriggerOnFileAvailable") in altre azioni del flusso. 

	**Nota:** dopo aver elaborato il file recuperato, il trigger SFTP "TriggerOnFileAvailable" lo eliminerà.

8.	Configurare le proprietà di input per il trigger SFTP nel modo indicato di seguito:

	- **Folder Path**: specificare il percorso della cartella da cui devono essere recuperati i file.
	- **The type of the file: text or binary**: selezionare il tipo di file.
	- **File Mask**: specificare la maschera di file da applicare per recuperare i file. '*' consente di recuperare tutti i file presenti nella cartella specificata.
	- **Exclude File Mask**: specificare la maschera di file da applicare per escludere i file. Se è impostata anche la proprietà "File Mask", Exclude File Mask verrà applicata per prima.

 
![][9] 
![][10]

7.	È possibile usare le azioni SFTP nel flusso in modo analogo. È possibile usare l'azione "Upload File" per caricare un file nel server SFTP. Configurare le proprietà di input per l'azione "Upload File" nel modo indicato di seguito:

	- **Content**: specificare il contenuto del file da caricare.
	- **Content Transfer Encoding**: specificare None o Base64.
	- **File Path**: specificare il percorso del file da caricare.
	- **Overwrite**: specificare "true" per sovrascrivere il file, se già esistente.
	- **Append If Exists **: specificare "true" o "false". Quando impostato su "true", i dati vengono aggiunti al file, se esistente. Se impostato su "false", il file, se esistente, viene sovrascritto.
	- **Temporary Folder**: se specificato, l'adattatore caricherà il file in 'Temporary Folder Path'. Una volta completato il caricamento, il file verrà spostato in 'Folder Path'. Per garantire che l'operazione di spostamento sia atomica, 'Temporary Folder Path' deve trovarsi nello stesso disco fisico di 'Folder Path'. È possibile usare Temporary Folder solo quando la proprietà 'Append If Exist' è disabilitata.

![][11]
![][12]





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

<!--HONumber=52-->