<properties 
   pageTitle="Connettore BLOB di Archiviazione di Azure" 
   description="Introduzione al connettore BLOB di Archiviazione di Azure" 
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
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
#Connettore BLOB di Archiviazione di Azure

##Panoramica
Il connettore BLOB di Archiviazione di Azure consente di caricare, scaricare ed eliminare i BLOB da un contenitore BLOB.

##Creare un nuovo connettore BLOB di Archiviazione di Azure
Per creare un nuovo connettore di Archiviazione di Azure, attenersi alla procedura riportata di seguito. <ul> <li>Avviare il portale di Azure <li>Aprire Azure Marketplace usando +Nuovo (nella parte inferiore della pagina) -> Web e dispositivi mobili --> Azure Marketplace </ul>

![Avviare Azure Marketplace][1]<br> <ul> <li>Fare clic su App per le API <li>Cercare <i>BLOB</i>, quindi selezionare Connettore BLOB di Archiviazione di Azure </ul>

![Selezionare il connettore BLOB Archiviazione di Azure][2] <br> <ul> <li>Fare clic su Crea <li>Nel pannello del connettore BLOB di Archiviazione di Azure che si apre specificare i dati seguenti. </ul>

![Creare il connettore BLOB Archiviazione di Azure][3]

- **Località**: scegliere la località geografica in cui si vuole distribuire il connettore
- **Sottoscrizione**: scegliere una sottoscrizione in cui si vuole creare questo connettore
- **Gruppo di risorse**: selezionare o creare un gruppo di risorse in cui deve risiedere il connettore
- **Piano di hosting Web**: selezionare o creare un piano di hosting Web
- **Piano tariffario**: scegliere un piano tariffario per il connettore
- **Nome**: assegnare un nome al connettore BLOB di Archiviazione
- **Impostazioni pacchetto** 
	- **URI SAS/contenitore**: specificare l'URI del contenitore BLOB. L'URI può includere anche il token SAS. Ad esempio http://storageaccountname.blob.core.windows.net/containername o http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **Chiave di accesso**: specificare una chiave di accesso valida dell'account di archiviazione primario/secondario. Lasciare vuoto questo campo se si sta usando un token SAS per l'autenticazione.
- Fare clic su Crea. Verrà creato un nuovo connettore BLOB di Archiviazione di Azure.

##Usare il connettore BLOB di Archiviazione di Azure in un'app per la logica
Il connettore BLOB di Archiviazione di Azure, una volta creato, può essere usato dal flusso.

Creare un nuovo flusso con +Nuovo -> Web e dispositivi mobili -> App per la logica. Specificare i metadati per il flusso, incluso il gruppo di risorse

![Creare un'app per la logica][4]

Fare clic su *Trigger e azioni*. Si apre la finestra di progettazione del flusso

![Finestra di progettazione del flusso vuoto di un'app per la logica][5]

Il connettore BLOB di Archiviazione di Azure può essere usato come azione.

###Actions
Fare clic su Azure Storage Blob Connector nel riquadro destro. Il connettore elenca le azioni supportate.

![Elenco di azioni del BLOB di Archiviazione di Azure][10]

Il connettore BLOB di Archiviazione di Azure supporta quattro azioni, ovvero

- **Ottieni BLOB**: ottiene un BLOB specifico dal contenitore.
- **Carica BLOB**: carica un nuovo BLOB o aggiorna un BLOB esistente.
- **Elimina BLOB**: elimina un BLOB specifico da un contenitore.
- **Elenca BLOB**: elenca tutti i BLOB in una directory.
- **Crea snapshot BLOB**: crea una snapshot di sola lettura di un BLOB specifico.
- **Copia BLOB**: crea un nuovo BLOB copiandolo da un altro BLOB. Il BLOB di origine può essere nello stesso account o in un altro account.

Si consideri come esempio l'azione Carica BLOB. Fare clic su Carica BLOB

![Input dell'azione Carica BLOB][11]


- **Percorso BLOB**: specifica il percorso del BLOB da caricare. Il percorso viene interpretato come relativo al percorso del contenitore configurato.
- **Contenuto in scrittura BLOB**: specifica il contenuto e le proprietà del BLOB da caricare.
- **Codifica trasferimento contenuto**: specifica Nessuna o Base64.
- **Sovrascrivi**: se impostato su true, il BLOB esistente verrà sovrascritto. In caso contrario, restituirà un errore se esiste già un BLOB nello stesso percorso.

Specificare gli input e fare clic sul segno di spunta per completare la configurazione degli input.


Si noti che l'azione Carica BLOB del BLOB di Archiviazione di Azure configurata mostra sia i parametri di input che i parametri di output.

####Uso degli output delle azioni precedenti come input delle azioni del BLOB di Archiviazione di Azure
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

<!--HONumber=54-->