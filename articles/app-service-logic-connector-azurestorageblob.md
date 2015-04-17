<properties 
   pageTitle="Connettore BLOB di Archiviazione di Azure" 
   description="Introduzione al connettore BLOB di Archiviazione di Azure" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
# Connettore BLOB di Archiviazione di Azure

## Informazioni generali
Il connettore BLOB di Archiviazione di Azure consente di caricare, scaricare ed eliminare i BLOB da un contenitore BLOB.

## Creare un nuovo connettore BLOB di Archiviazione di Azure
Per creare un nuovo connettore di Archiviazione di Azure, attenersi alla procedura riportata di seguito.
- Avviare il portale di Azure
- Aprire Azure Marketplace usando +Nuovo (nella parte inferiore della pagina) -> Web e dispositivi mobili --> Azure Marketplace

![Launch Azure Marketplace][1]
- Fare clic su App per le API
- Cercare _BLOB_ e selezionare Azure Storage Blob Connector

![Select Azure Storage Blob Connector][2]
- Fare clic su Crea.
- Nel pannello del connettore BLOB di Archiviazione di Azure che si apre specificare i dati seguenti.

![Create Azure Storage Blob Connector][3]

- **Località**: scegliere la località geografica in cui si vuole distribuire il connettore
- **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
- **Gruppo di risorse**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
- **Piano tariffario**: scegliere un piano tariffario per il connettore
- **Nome**: assegnare un nome al connettore FTP
- **Impostazioni pacchetto** 
	- **URI SAS/contenitore**: specificare l'URI del contenitore BLOB. L'URI può includere anche il SAS token. Esempio http://storageaccountname.blob.core.windows.net/containername or http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **Chiave di accesso**: specificare una chiave di accesso valida dell'account di archiviazione primario/secondario. Lasciare vuoto questo campo se si sta usando un token SAS per l'autenticazione.
- Fare clic su Crea. Verrà creato un nuovo connettore BLOB di Archiviazione di Azure.

## Usare il connettore BLOB di Archiviazione di Azure in un'app per la logica
Il connettore BLOB di Archiviazione di Azure, una volta creato, può essere usato dal flusso.

Creare un nuovo flusso con +Nuovo -> Web e dispositivi mobili -> App per la logica. Specificare i metadati per il flusso, incluso il gruppo di risorse

![Create Logic App][4]

Fare clic su *triggers and actions*. Si apre la finestra di progettazione del flusso

![Logic App empty flow designer][5]

Il connettore BLOB di Archiviazione di Azure può essere usato sia come trigger che come azione. 

### Trigger
Nella finestra di progettazione del flusso vuota fare clic su Azure Storage Blob Connector nel riquadro della raccolta a destra.

![Choose Blob Available Trigger][6]

Il connettore BLOB di Archiviazione di Azure ha un trigger, _BLOB disponibile_. Questo trigger esegue il polling per i BLOB presenti in un determinato contenitore. Sono supportati anche il polling a livello di directory e il filtro dei BLOB. I BLOB, una volta recuperati, vengono eliminati dal contenitore

Fare clic sul trigger _BLOB disponibile_.

![Basic inputs Blob Available Trigger][7]

Gli input consentono di configurare un determinato percorso cartella di cui eseguire il polling in base a una frequenza pianificata. Gli input di base sono
- Frequenza: specifica la frequenza del polling FTP
- Intervallo: specifica l'intervallo per la frequenza pianificata
- Percorso cartella: specifica un percorso cartella virtuale di cui eseguire il polling. Usare "." per la cartella del contenitore radice
- Tipo file: specifica una maschera di file da confrontare con i nomi file dei BLOB.  Solo i BLOB con nomi file corrispondenti a questa maschera di file verranno inclusi nel polling. Per impostazione predefinita, verranno inclusi tutti i BLOB.

Facendo clic su ... vengono visualizzati gli input avanzati. 

![Advanced inputs Blob Available Trigger][8]

Gli input avanzati sono

- Maschera file: specifica una maschera di file da confrontare con i nomi file dei BLOB.  Solo i BLOB con nomi file corrispondenti a questa maschera di file verranno inclusi nel polling. Per impostazione predefinita, verranno inclusi tutti i BLOB.
- Escludi maschera file: specifica una maschera di file da confrontare con i nomi file dei BLOB.  I BLOB corrispondenti a questa maschera di file verranno esclusi. Per impostazione predefinita, nessun BLOB viene escluso.

Specificare gli input e fare clic sul segno di spunta per completare la configurazione degli input.

![Configured Blob Available Trigger][9]

Si noti che il trigger _BLOB disponibile_ configurato mostra sia i parametri di input configurati che i parametri di output. 

Una volta creata l'app per la logica, il trigger _BLOB disponibile_ 


- Esegue il polling della cartella percorso per i nuovi file
- Crea un'istanza del flusso logico in qualsiasi momento per ogni nuovo file
- Elimina il file dal percorso cartella, una volta creata l'istanza del flusso logico

#### Uso dell'output del trigger BLOB disponibile in azioni successive
L'output del trigger _BLOB disponibile_ può essere usato come input di alcune altre azioni nel flusso. 

È possibile fare clic su + nella finestra di dialogo di input dell'azione e selezionare l'output dell'FTP direttamente dalla casella di riepilogo.

È anche possibile scrivere un'espressione direttamente nella casella di input dell'azione. Di seguito è riportata l'espressione del flusso per fare riferimento all'output del trigger

	@triggers().outputs.body.Content

### Azioni
Fare clic su Azure Storage Blob Connector nel riquadro destro. Il connettore elenca le azioni supportate.

![List of Azure Storage Blob Actions][10]

Il connettore BLOB di Archiviazione di Azure supporta quattro azioni, ovvero

- **Ottieni BLOB**: ottiene un BLOB specifico dal contenitore
- **Carica BLOB**: carica un nuovo BLOB o aggiorna un BLOB esistente
- **Elimina BLOB**: elimina un BLOB specifico da un contenitore
- **Elenca BLOB**: elenca tutti i BLOB in una directory
- **Crea snapshot BLOB**: crea una snapshot di sola lettura di un BLOB specifico
- **Copia BLOB**: crea un nuovo BLOB copiandolo da un altro BLOB.  Il BLOB di origine può essere nello stesso account o in un altro account.

Si consideri come esempio l'azione Carica BLOB. Fare clic su Carica BLOB

![Inputs of Upload Blob action][11]


- **Percorso BLOB**: specifica il percorso del BLOB da caricare.  Il percorso viene interpretato come relativo al percorso del contenitore configurato.
- **Contenuto in scrittura BLOB**: specifica il contenuto e le proprietà del BLOB da caricare.
- **Codifica trasferimento contenuto**: specifica Nessuna o Base 64
- **Sovrascrivi**: se impostato su true, il BLOB esistente verrà sovrascritto. In caso contrario, restituirà un errore se esiste già un BLOB nello stesso percorso.

Specificare gli input e fare clic sul segno di spunta per completare la configurazione degli input.


Si noti che l'azione Carica BLOB del BLOB di Archiviazione di Azure configurata mostra sia i parametri di input che i parametri di output.

#### Uso degli output delle azioni precedenti come input dell'azione FTP
Si noti che nella schermata configurata il valore del contenuto è impostato su un'espressione.

	@triggers().outputs.body.Content


È possibile impostarlo su qualsiasi valore. Questo è solo un esempio. L'espressione accetta l'output del trigger dell'app per la logica e lo usa come contenuto del file da caricare. Si supponga di voler usare l'output di un'azione precedente, ad esempio un'azione di trasformazione. In questo caso, l'espressione sarà

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG

<!--HONumber=49-->