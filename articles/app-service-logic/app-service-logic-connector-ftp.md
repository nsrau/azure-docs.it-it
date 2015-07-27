<properties
	pageTitle="Connettore FTP"
	description="Introduzione al connettore FTP"
	authors="rajram"
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
	ms.date="07/01/2015"
	ms.author="rajram"/>

#Connettore FTP

##Panoramica
Il connettore FTP consente di spostare dati da/a un server FTP. Le principali funzionalità del connettore FTP sono:

- Pull di file dal server FTP su richiesta
- Esecuzione di poll in base a una pianificazione configurabile
- Poll del server FTP e attivazione del flusso logico in base ai nuovi documenti sul server FTP
- Specifica del server FTP come indirizzo IP, porta, password e nome host
- Possibilità di eseguire invii su richiesta
- Possibilità di eliminare file sul server FTP su richiesta

##Creare un nuovo connettore FTP
Per creare un nuovo connettore FTP, attenersi alla procedura riportata di seguito. - Avviare il portale di Azure - Aprire Azure Marketplace usando +Nuovo (nella parte inferiore della pagina) -> Web e dispositivi mobili --> Azure Marketplace

	![Launch Azure Marketplace][1]

- Fare clic su App per le API
- Cercare FTP e selezionare FTP Connector

	![Selezionare il connettore FTP][2]

- Fare clic su Crea.
- Nel pannello del connettore FTP che si apre specificare i dati seguenti.

	![Creare il connettore FTP][3]

- **Località**: scegliere la località geografica in cui si vuole distribuire il connettore
- **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
- **Gruppo di risorse**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
- **Piano tariffario**: scegliere un piano tariffario per il connettore
- **Nome**: assegnare un nome al connettore FTP
- **Impostazioni pacchetto**
	- **Indirizzo server**: specificare il nome del server FTP o l'indirizzo IP
	- **Nome utente**: specificare il nome utente per la connessione al server FTP
	- **Password**: specificare la password per la connessione al server FTP
	- **Cartella radice**: specificare il percorso della cartella radice.
	- **Usa binario**: specificare true per usare la modalità di trasferimento binario, false per ASCII
	- **Usa SSL**: specificare true per usare FTP su un canale SSL/TLS sicuro
	- **Porta server**: specificare il numero di porta del server FTP
- Fare clic su Crea. Verrà creato un nuovo connettore FTP.

##Usare il connettore FTP nell'app per la logica
Una volta creato, il connettore FTP può essere usato dal flusso.

Creare un nuovo flusso con +Nuovo -> Web e dispositivi mobili -> App per la logica. Specificare i metadati per il flusso, incluso il gruppo di risorse

![Creare un'app per la logica][4]

Fare clic su *Trigger e azioni*. Si apre la finestra di progettazione del flusso

![Finestra di progettazione del flusso vuoto di un'app per la logica][5]

Il connettore FTP può essere usato sia come trigger che come azione.

###Trigger
Nella finestra di progettazione del flusso vuota fare clic su FTP Connector nel riquadro della raccolta a destra.

![Scegliere un trigger FTP][6]

Il connettore FTP include un trigger - 'File Available (Read then Delete)'. Questo trigger

- Esegue il polling della cartella percorso per i nuovi file
- Crea un'istanza del flusso logico in qualsiasi momento per ogni nuovo file
- Elimina il file dal percorso cartella, una volta creata l'istanza del flusso logico

Fare clic sul trigger 'File Available (Read then Delete)'.

![Trigger FTP per input di base][7]

Gli input consentono di configurare un determinato percorso cartella di cui eseguire il polling in base a una frequenza pianificata. Gli input di base sono i seguenti: - Frequenza: specifica la frequenza del polling FTP - Intervallo: specifica l'intervallo per la frequenza pianificata - Percorso cartella: specifica il percorso cartella sul server FTP - Tipo di file: specifica se il file è di tipo testo o binario

Per visualizzare gli input avanzati, fare clic sui puntini di sospensione '...'.

![Trigger FTP per input di base][8]

Gli input avanzati sono i seguenti: - Maschera file: specifica la maschera di file durante il polling - Escludi maschera file: specifica le maschere di file da escludere durante il polling

Specificare gli input e fare clic sul segno di spunta per completare la configurazione degli input.

![Trigger FTP per input di base][9]

Si noti che il trigger FTP configurato mostra sia i parametri di input configurati che i parametri di output.

####Uso dell'output del trigger FTP in azioni successive
L'output del connettore FTP può essere usato come input di alcune altre azioni nel flusso.

È possibile fare clic sui puntini di sospensione '...' nella finestra di dialogo di input dell'azione e selezionare l'output dell'FTP direttamente dalla casella di riepilogo a discesa.

È anche possibile scrivere un'espressione direttamente nella casella di input dell'azione. Di seguito è riportata l'espressione del flusso per fare riferimento all'output del trigger FTP

	@triggers('ftpconnector').outputs.body.Content

###Azioni
Fare clic su FTP Connector nel riquadro destro. Il connettore FTP elenca le azioni supportate.

![Elenco di azioni FTP][10]

Il connettore FTP supporta quattro azioni, ovvero

- **Get File**: recupera i contenuti di un file specifico
- **Upload File**: carica un file nel percorso cartella FTP
- **Delete File**: elimina un file dal percorso cartella FTP
- **List Files**: elenca tutti i file nel percorso cartella FTP

Si consideri come esempio l'azione Upload File. Fare clic su Upload File

Vengono visualizzati prima gli input di base.

![Input di base dell'azione Carica file][11]


- **Content**: specificare il contenuto del file da caricare.
- **Content Transfer Encoding**: specificare none o base64.
- **File Path**: specificare il percorso del file da caricare.

Fare clic su ... per gli input avanzati.

![Input di base dell'azione Carica file][12]


- **Append If Exists**: può essere True o False. Se abilitato, i dati vengono aggiunti al file, se esiste. Se disabilitato, il file viene sovrascritto, se esiste
- **Temporary Folder**: facoltativo. Se specificato, l'adattatore caricherà il file in Temporary Folder Path. Al termine del caricamento, il file verrà spostato in Folder Path. Per garantire che l'operazione di spostamento sia atomica, Temporary Folder Path deve trovarsi nello stesso disco fisico di Folder Path. È possibile usare Temporary Folder solo quando la proprietà Aggiungi se esiste è disabilitata.

Specificare gli input e fare clic sul segno di spunta per completare la configurazione degli input.

![Azione Carica file configurata][13]

Il parametro 'File Path' è impostato su

	@concat('/Output/',triggers().outputs.body.FileName)

Si noti che l'azione Carica file dell'FTP configurata mostra sia i parametri di input che i parametri di output.

####Uso degli output delle azioni precedenti come input dell'azione FTP
Si noti che nella schermata configurata il valore del contenuto è impostato su un'espressione.

	@triggers().outputs.body.Content


È possibile impostarlo su qualsiasi valore. Questo è solo un esempio. L'espressione accetta l'output del trigger dell'app per la logica e lo usa come contenuto del file da caricare. Si supponga di voler usare l'output di un'azione precedente, ad esempio un'azione di trasformazione. In questo caso, l'espressione sarà

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=July15_HO3-->