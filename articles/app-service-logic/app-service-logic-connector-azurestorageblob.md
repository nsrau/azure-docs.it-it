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
   ms.date="06/30/2015"
   ms.author="rajram"/>
   
# Azure Storage Blob Connector
Connette il BLOB di archiviazione di Azure per caricare, scaricare ed eliminare i BLOB da un contenitore BLOB. I connettori possono essere usati nelle app per la logica come parte di un "flusso di lavoro".

## Trigger e azioni
I *trigger* sono eventi che si verificano, ad esempio quando si aggiorna un ordine o quando viene aggiunto un nuovo cliente. Un'*azione* è il risultato del trigger. Quando ad esempio si aggiorna un ordine, viene inviato un avviso al venditore oppure quando si aggiunge un nuovo cliente, viene inviato un messaggio di benvenuto.

Storage Blob Connector può essere usato come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Attualmente non sono disponibili trigger per Storage Blob Connector.

Per Storage Blob Connector sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Get Blob: ottiene un BLOB specifico dal contenitore</li><li>Upload Blob: carica un nuovo BLOB o ne aggiorna uno esistente</li><li>Delete Blob: elimina un BLOB specifico da un contenitore</li><li>List Blobs: elenca tutti i BLOB in una directory</li><li>Snapshot Blob: crea uno snapshot di sola lettura di un BLOB specifico</li><li>Copy Blob: crea un nuovo BLOB copiandolo da un altro BLOB. Il BLOB di origine può trovarsi nello stesso account o in un altro account.</li></ul>


## Creare Azure Storage Blob Connector

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "BLOB": <br/> ![Selezionare il connettore BLOB Archiviazione di Azure][2]

3. **Creare** il connettore.
4. Immettere il nome, il piano di servizio app e altre proprietà.
5. Immettere le impostazioni pacchetto seguenti:

	Nome | Obbligatorio | Descrizione
--- | --- | ---
Container/SAS URI | Sì | Immettere l'URI del contenitore BLOB. L'URI può includere anche il token SAS. Ad esempio, immettere: http://*storageaccountname*.blob.core.windows.net/containername o http://*storageaccountname*.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
Access Key | No | Immettere una chiave di accesso valida dell'account di archiviazione primario o secondario. Se si sta usando un token di firma di accesso condiviso per l'autenticazione, lasciare vuoto questo campo.

	![Creare il connettore BLOB Archiviazione di Azure][3]

6. Fare clic su **Crea**.

## Usare il connettore BLOB di Archiviazione di Azure in un'app per la logica
Dopo aver creato Azure Storage Blob Connector, è possibile aggiungerlo al flusso di lavoro.

1. Creare una nuova app per la logica con +Nuovo -> Web e dispositivi mobili -> App per la logica. Immettere le proprietà dell'app per la logica.

	![Creare un'app per la logica][4]

2. Fare clic su **Trigger e azioni**. Viene aperta la finestra di progettazione del flusso di lavoro:

	![Finestra di progettazione del flusso vuoto di un'app per la logica][5]

3. Selezionare Azure Storage Blob Connector nel riquadro destro. Il connettore elenca le azioni disponibili:

	![Elenco di azioni del BLOB di Archiviazione di Azure][10]

4. In questo scenario si userà l'azione **Upload Blob**:

	![Input dell'azione Carica BLOB][11]

5. Immettere i valori di input e selezionare il segno di spunta per completare la configurazione:

	Input | Descrizione
--- | ---
Blob Path | Determina il percorso del BLOB da caricare. Il percorso viene interpretato come relativo al percorso del contenitore configurato.
Blob Write Content | Immettere il contenuto e le proprietà del BLOB da caricare.
Content Transfer Encoding | Immettere None o Base64.
Overwrite | Quando il valore è impostato su true, il BLOB esistente viene sovrascritto. Quando è impostato su false, restituirà un errore se esiste già un BLOB nello stesso percorso.

Si noti che l'azione Carica BLOB del BLOB di Archiviazione di Azure configurata mostra sia i parametri di input che i parametri di output.

#### Uso degli output delle azioni precedenti come input delle azioni del BLOB di Archiviazione di Azure
Nella schermata precedente il valore **Content** può essere un'espressione:

	@triggers().outputs.body.Content

È possibile impostarlo su qualsiasi valore. L'espressione accetta l'output del trigger dell'app per la logica e lo usa come contenuto del file da caricare. Ad esempio, si vuole usare l'output di una trasformazione. In questo scenario l'espressione sarà:

	@actions('transformservice').outputs.body.OutputXML

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
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
 

<!---HONumber=July15_HO3-->