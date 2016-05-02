<properties 
   pageTitle="Uso del connettore AS2 nelle app per la logica | Microsoft Azure App Service" 
   description="Come creare e configurare l'app per le API o il connettore AS2 e usarlo in un'app per la logica in Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Uso del connettore AS2 e aggiunta all'app per la logica

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Usare il connettore AS2 per ricevere e inviare messaggi mediante il protocollo di trasporto AS2 (Applicability Statement 2) nelle comunicazioni B2B (Business to Business). I dati vengono trasportati in modo sicuro e affidabile tramite Internet. La sicurezza è garantita con i certificati digitali e la crittografia.

È possibile aggiungere il connettore AS2 al flusso di lavoro aziendale ed elaborare i dati come parte di un flusso di lavoro Business to Business nell'ambito di un'app per la logica.

## Trigger e azioni
Un trigger avvia una nuova istanza in base a un evento specifico, ad esempio l'arrivo di un messaggio AS2 da un partner. Un'azione è il risultato, ad esempio, dopo la ricezione di un messaggio AS2 poi viene inviato il messaggio tramite AS2.

Il connettore AS2 può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per il connettore AS2 sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Receive & Decode | Encode & Send

## Requisiti iniziali
L'utente deve creare questi elementi prima di poterli usare nel connettore AS2:

Requisito | Descrizione
--- | ---
App per le API TPM | Prima di creare AS2 Connector , è necessario creare un [connettore BizTalk Trading Partner Management][1]. <br/><br/>**Nota** Prendere nota del nome dell'app per le API TPM. 
Database SQL di Azure | Archivia elementi B2B, tra cui partner, schemi, certificati e contratti. Ogni app per le API B2B richiede un proprio database SQL di Azure. <br/><br/>**Nota** Copiare la stringa di connessione al database.<br/><br/>[Creare un database SQL di Azure](../sql-database/sql-database-get-started.md)
Contenitore dell'archiviazione BLOB di Azure | Archivia le proprietà dei messaggi quando è abilitata l'archiviazione AS2. Se l'archiviazione dei messaggi AS2 non è necessaria, non lo è nemmeno il contenitore di archiviazione. <br/><br/>**Nota** Se si sta abilitando l'archiviazione, copiare la stringa di connessione all'archivio BLOB.<br/><br/>[Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md)

## Creare il connettore AS2

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Cercare "AS2 Connector", selezionarlo e fare clic su **Crea**.
3. Immettere il nome, il piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Proprietà | Descrizione
--- | --- 
Database Connection String | Immettere la stringa di connessione ADO.NET al database SQL di Azure creato. Quando si copia la stringa di connessione, la password non viene aggiunta alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione prima di incollare.
Enable Archiving for incoming messages | Facoltativa. Abilitare questa proprietà per archiviare le proprietà di un messaggio AS2 in arrivo ricevuto da un partner. 
Azure Blob Storage Connection String | Immettere la stringa di connessione per il contenitore di archiviazione BLOB di Azure creato. Quando è abilitata l'archiviazione, i messaggi codificati e decodificati vengono archiviati nel contenitore di archiviazione.
TPM Instance Name | Immettere il nome dell'app per le API **BizTalk Trading Partner Management** creata in precedenza. Quando si crea il connettore AS2, il connettore esegue solo i contratti AS2 all'interno di questa specifica istanza di TPM.

5. Selezionare **Create**.

I partner commerciali sono le entità coinvolte nelle comunicazioni B2B. Quando due partner stabiliscono una relazione, questa è definita Contratto. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto.

Fare riferimento alla procedura per [creare un contratto tra partner commerciali][2].

## Usare il connettore come trigger

1. Quando si crea o si modifica un'app per la logica, nel pannello a destra selezionare AS2 Connector creato in precedenza: ![Impostazioni di trigger][3]

2. Fare clic sulla freccia destra →: ![Opzioni di trigger][4]

3. Il connettore AS2 espone un singolo trigger. Selezionare *Receive & Decode*: ![Input di Receive & Decode][5]

4. Questo trigger non include input. Fare clic sulla freccia destra →: ![Configurazione di Receive & Decode][6]

Come parte dell'output, il connettore restituisce il payload AS2, nonché i metadati specifici di AS2.

Il Trigger viene attivato quando un Payload AS2 è come un POST alhttps://{HostURL} / decodifica. È possibile trovare l'URL host nelle impostazioni dell'app per le API. Inoltre può essere necessario modificare il livello di accesso dell'app per le API nelle impostazioni dell'applicazione in Pubblico (autenticato o anonimo).

## Usare il connettore come azione
1. Dopo il trigger (o dopo aver scelto di eseguire la logica manualmente), dal pannello a destra aggiungere il connettore AS2 creato in precedenza: ![Impostazioni di azione][7]

2. Fare clic sulla freccia destra →: ![Elenco di azioni][8]

3. Il connettore AS2 supporta una sola azione. Selezionare *Encode & Send*: ![Input di Encode & Send][9]

4. Immettere gli input per l'azione e configurarla: ![Configurazione di Encode & Send][10]

	I parametri includono:

	Parametro | Tipo | Descrizione
--- | --- | ---
Payload | object| Contenuto del payload da codificare e registrare nell'endpoint configurato. Il payload deve essere specificato come oggetto JSON.
AS2 From | string | Identità AS2 del mittente del messaggio AS2. Questo parametro viene usato per cercare il contratto appropriato per l'invio del messaggio.
AS2 To | string | Identità AS2 del destinatario del messaggio AS2. Questo parametro viene usato per cercare il contratto appropriato per l'invio del messaggio.
Partner URL | string | Endpoint del partner a cui deve essere inviato il messaggio.
Enable Archiving | boolean | Determina se il messaggio in uscita deve essere archiviato.

L'azione restituisce un codice di risposta HTTP 200 al termine dell'esecuzione.

## Altri vantaggi del connettore
È possibile [archiviare i messaggi AS2](app-service-logic-archive-as2-messages.md).

Altre informazioni sulle app per la logica sono disponibili nell'articolo [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Se si vuole iniziare a usare le app per la logica di Azure prima di creare un account di Azure, [provare le app per la logica](https://tryappservice.azure.com/?appservice=logic). È possibile creare immediatamente un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=AcomDC_0420_2016-->