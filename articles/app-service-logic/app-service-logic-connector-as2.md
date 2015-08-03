<properties 
   pageTitle="Uso di AS2 Connector nel servizio app di Microsoft Azure" 
   description="Come usare AS2 Connector" 
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
   ms.date="06/29/2015"
   ms.author="rajram"/>

# Microsoft AS2 Connector
Con Microsoft Azure AS2 Connector è possibile ricevere e inviare messaggi tramite il protocollo di trasporto AS2 (Applicability Statement 2) nelle comunicazioni Business to Business. I dati vengono trasportati in modo sicuro e affidabile tramite Internet. La sicurezza è garantita con i certificati digitali e la crittografia.

## Trigger e azioni
Un trigger avvia una nuova istanza in base a un evento specifico, ad esempio l'arrivo di un messaggio AS2 da un partner. Un'azione è il risultato, ad esempio, dopo la ricezione di un messaggio AS2 poi viene inviato il messaggio tramite AS2.

AS2 Connector può essere usato come trigger o come azione in un'app per la logica e supporta i dati nei formati JSON e XML. Per AS2 Connector sono disponibili i trigger e le azioni seguenti:

Trigger | Azioni
--- | ---
Receive & Decode | Encode & Send

## Requisiti iniziali
L'utente deve creare questi elementi prima di poterli usare in AS2 Connector:

Requisito | Descrizione
--- | ---
App per le API TPM | Prima di creare AS2 Connector , è necessario creare un [connettore BizTalk Trading Partner Management][1]. <br/><br/>**Nota** Prendere nota del nome dell'app per le API TPM. 
Database SQL di Azure | Archivia elementi B2B, tra cui partner, schemi, certificati e contratti. Ogni app per le API B2B richiede un proprio database SQL di Azure. <br/><br/>**Nota** Copiare la stringa di connessione al database.<br/><br/>[Creare un database SQL di Azure](../sql-database-create-configure.md)
Contenitore dell'archiviazione BLOB di Azure | Archivia le proprietà dei messaggi quando è abilitata l'archiviazione AS2. Se l'archiviazione dei messaggi AS2 non è necessaria, non lo è nemmeno il contenitore di archiviazione. <br/><br/>**Nota** Se si sta abilitando l'archiviazione, copiare la stringa di connessione all'archivio BLOB.<br/><br/>[Informazioni sugli account di archiviazione di Azure](../storage-create-storage-account.md)

## Creare AS2 Connector

È possibile creare un connettore nell'ambito di un'app per la logica oppure crearlo direttamente da Azure Marketplace. Per creare un connettore da Marketplace:

1. Nella Schermata iniziale di Azure selezionare **Marketplace**.
2. Selezionare **App per le API** e cercare "AS2 Connector".
3. Immettere il nome, il piano di servizio app e altre proprietà.
4. Immettere le impostazioni pacchetto seguenti:

	Proprietà | Descrizione
--- | --- 
Stringa di connessione a database | Immettere la stringa di connessione ADO.NET al database SQL di Azure creato. Quando si copia la stringa di connessione, la password non viene aggiunta alla stringa di connessione. Assicurarsi di immettere la password nella stringa di connessione prima di incollare.
Abilita archiviazione per messaggi in arrivo | Facoltativa. Abilitare questa proprietà per archiviare le proprietà di un messaggio AS2 in arrivo ricevuto da un partner. 
Stringa di connessione ad archiviazione BLOB di Azure | Immettere la stringa di connessione per il contenitore di archiviazione BLOB di Azure creato. Quando è abilitata l'archiviazione, i messaggi codificati e decodificati vengono archiviati nel contenitore di archiviazione.
Nome istanza TPM | Immettere il nome dell'app per le API **BizTalk Trading Partner Management** creata in precedenza. Quando si crea il connettore AS2, il connettore esegue solo i contratti AS2 all'interno di questa specifica istanza di TPM.

5. Selezionare **Crea**.

I partner commerciali sono le entità coinvolte nelle comunicazioni B2B (Business to Business). Quando due partner stabiliscono una relazione, questa è definita Contratto. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto.

I passaggi per la creazione di un accordo tra partner commerciali sono documentati [qui][2].

## Usare il connettore come trigger

1. Quando si crea o si modifica un'app per la logica, nel riquadro destro selezionare AS2 Connector creato in precedenza: <br/> ![Impostazioni di trigger][3]

2. Fare clic sulla freccia destra →: <br/> ![Opzioni di trigger][4]

3. AS2 Connector espone un singolo trigger. Selezionare *Receive & Decode*: <br/> ![Input di Receive & Decode][5]

4. Questo trigger non include input. Fare clic sulla freccia destra →: <br/> ![Configurazione di Receive & Decode][6]

Quale parte dell'output, il connettore restituisce il payload AS2, nonché i metadati specifici di AS2.

Il Trigger viene attivato quando un Payload AS2 è come un POST alhttps://{HostURL} / decodifica. È possibile trovare l'URL Host nell'API App impostazioni. Inoltre potrebbe essere necessario modificare il livello di accesso dell'applicazione nelle impostazioni dell'applicazione API pubblico (autenticate o anonime).

## Usare il connettore come azione
1. Dopo il trigger (o dopo aver scelto di eseguire la logica manualmente), dal riquadro destro aggiungere AS2 Connector creato in precedenza: <br/> ![Impostazioni di azione][7]

2. Fare clic sulla freccia destra →: <br/> ![Elenco di azioni][8]

3. AS2 Connector supporta una sola azione. Selezionare *Encode & Send*: <br/> ![Input di Encode & Send][9].

4. Immettere gli input per l'azione e configurarla: <br/> ![Configurazione di Encode & Send][10]

I parametri includono:

Parametro | Tipo | Descrizione
--- | --- | ---
Payload | oggetto| Contenuto del payload da codificare e registrare nell'endpoint configurato. Il payload deve essere specificato come oggetto JSON.
AS2 di origine | stringa | Identità AS2 del mittente del messaggio AS2. Questo parametro viene usato per cercare il contratto appropriato per l'invio del messaggio.
AS2 di destinazione | stringa | Identità AS2 del destinatario del messaggio AS2. Questo parametro viene usato per cercare il contratto appropriato per l'invio del messaggio.
URL del partner | stringa | Endpoint del partner a cui deve essere inviato il messaggio.
Abilita l'archiviazione | booleano | Determina se il messaggio in uscita deve essere archiviato.

L'azione restituisce un codice di risposta HTTP 200 al termine dell'esecuzione.

## Più vantaggi con il connettore
Altre informazioni sulle app per la logica sono disponibili nell'articolo [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Creare app per le API con le API REST. Vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

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

<!---HONumber=July15_HO4-->