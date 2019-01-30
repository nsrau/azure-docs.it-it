---
title: Connettersi a sistemi SAP con le App per la logica di Azure | Microsoft Docs
description: Come accedere e gestire le risorse SAP grazie all'automazione dei flussi di lavoro con le App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 09/14/2018
tags: connectors
ms.openlocfilehash: 27da87c11ca35be72690965a2359ff6ff6b9f999
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391272"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Connettersi a sistemi SAP con App per la logica di Azure

Questo articolo illustra come accedere alle risorse SAP locali all'interno di un'app per la logica mediante il connettore SAP ERP Central Component (ECC). Il connettore SAP ECC supporta l'integrazione di messaggi o dati da e verso i sistemi basati su SAP Netweaver tramite Intermediate Document (IDoc) o Business Application Programming Interface (BAPI) o Remote Function Call (RFC).

Il connettore SAP ECC usa la <a href="https://support.sap.com/en/product/connectors/msnet.html">libreria SAP .Net Connector (NCo)</a> e fornisce le seguenti operazioni o azioni:

- **Inviare a SAP**: inviare IDoc o chiamare funzioni BAPI su tRFC nei sistemi SAP.
- **Ricevere da SAP**: ricevere IDoc o chiamate di funzioni BAPI su tRFC provenienti da sistemi SAP.
- **Generare schemi**: generare schemi per gli artefatti SAP per IDoc o BAPI o RFC.

Il connettore SAP si integra con i sistemi SAP locali tramite il [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127). Negli scenari di invio, ad esempio, quando si invia un messaggio dall'App per la logica a un sistema SAP, il gateway dati agisce come un client di RFC e inoltra le richieste ricevute dall'App per la logica a SAP.
Analogamente, negli scenari di ricezione, il gateway dati funge da server RFC che riceve le richieste da SAP e inoltra all'App per la logica. 

Questo articolo spiega come creare esempi di app per la logica che si integrano con SAP, illustrando allo stesso tempo gli scenari di integrazione descritti in precedenza.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questo articolo, sono necessari questi elementi:

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">registrarsi per creare un account Azure gratuito</a>.

* l'app per la logica da cui si desidera accedere al sistema SAP e un trigger che avvia il flusso di lavoro dell'app per la logica. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Il <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">server applicazioni SAP</a> o il <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">server di messaggistica SAP</a>

* Scaricare e installare la versione più recente del [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) in qualsiasi computer locale. Prima di proseguire, assicurarsi di configurare il gateway nel portale di Azure. Il gateway consente di accedere in modo sicuro ai dati e alle risorse locali. Per altre informazioni, vedere [Installare il gateway dati locale per le App per la logica di Azure](../logic-apps/logic-apps-gateway-install.md).

* Scaricare e installare la libreria client SAP più recente, che è attualmente <a href="https://softwaredownloads.sap.com/file/0020000001865512018" target="_blank">Connettore SAP (NCo) 3.0.21.0 per Microsoft .NET Framework 4.0 e Windows a 64 bit (x64)</a>, nello stesso computer del gateway dati locale. Installare questa versione o versioni successive per i motivi seguenti:

  * Le versioni precedenti di NCo SAP possono diventare deadlock quando vengono inviati più messaggi IDoc contemporaneamente. 
  Questa condizione blocca tutti i messaggi successivi che vengono inviati alla destinazione SAP, causando il timeout dei messaggi.

  * Il gateway dati locale viene eseguito solo su sistemi a 64 bit. 
  In caso contrario, viene visualizzato l'errore "immagine non valida" perché il servizio host del gateway dati non supporta gli assembly a 32 bit.

  * Il servizio host del gateway dati e l'adattatore SAP Microsoft usano .NET Framework 4.5. SAP NCo per .NET Framework 4.0 funziona con processi che usano runtime .NET dalla versione 4.0 alla 4.7.1. 
  NCo SAP per .NET Framework 2.0 funziona con processi che usano il runtime .NET da 2.0 a 3.5 e non funziona più con il gateway dati locale più recente.

* Contenuto del messaggio che è possibile inviare al server SAP, ad esempio un file di esempio IDoc. Il contenuto deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si desidera usare.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Inviare a SAP

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. L'app per la logica invia un documento intermedio (IDoc) a un server SAP e restituisce una risposta al richiedente che ha chiamato l'app per la logica. 

### <a name="add-http-request-trigger"></a>Aggiungere un trigger della richiesta HTTP

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

In questo esempio, viene creata un'app per la logica con un endpoint in Azure in modo che sia possibile inviare *richieste HTTP POST* all'app per la logica. Quando l'app per la logica riceve le richieste HTTP, il trigger viene attivato ed esegue il passaggio successivo nel flusso di lavoro.

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica vuota, che apre la Progettazione app per la logica. 

2. Nella casella di ricerca, digitare "richiesta http" come filtro. Nell'elenco di trigger selezionare questo trigger: **Richiesta - Alla ricezione di una richiesta HTTP**

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Salvare l'app per la logica per poter generare un URL endpoint per l'app per la logica.
Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Aggiungere l'azione SAP

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. Se non è ancora stato aggiunto un trigger all'app per la logica e si desidera seguire questo esempio, [aggiungere il trigger descritto in questa sezione](#add-trigger).

1. In Progettazione app per la logica, sotto il trigger, scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

   ![Aggiungere un'azione](./media/logic-apps-using-sap-connector/add-action.png) 

2. Nella casella di ricerca, immettere "sap" come filtro. Nell'elenco di azioni selezionare questa azione: **Send message to SAP** (Invia messaggio a SAP)
  
   ![Selezionare l'azione di invio a SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   In alternativa, invece di una ricerca, scegliere la scheda **Enterprise** e selezionare l'azione SAP.

   ![Selezionare l'azione di invio a SAP dalla scheda Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. Se vengono richiesti i dettagli della connessione, creare ora la connessione SAP. In caso contrario, se la connessione esiste già, continuare con il passaggio successivo per impostare l'azione SAP. 

   **Creare una connessione SAP locale**

   1. Specificare le informazioni di connessione per il server SAP. 
   Per la proprietà **Gateway dati**, selezionare il gateway dati creato nel portale di Azure per l'installazione del gateway.

      Se la proprietà **Tipo di accesso** è impostata su **Server applicazioni**, sono necessarie le seguenti proprietà (in genere facoltative):

      ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se la proprietà **Tipo di accesso** è impostata su **Gruppo**, sono necessarie le seguenti proprietà (in genere facoltative): 

      ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Al termine dell'operazione, scegliere **Crea**. 
   
      Le App per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente.

4. Ora individuare e selezionare un'azione dal server SAP. 

   1. Nella casella **Azione SAP**, scegliere l'icona cartella. 
   Dall'elenco file, individuare e selezionare il messaggio SAP che si desidera usare. 
   Per spostarsi nell'elenco, usare le frecce.

      In questo esempio viene selezionato un IDoc di tipo **Ordine**. 

      ![Trovare e selezionare l'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se non è possibile trovare l'azione desiderata, è possibile immettere manualmente un percorso, ad esempio:

      ![Specificare manualmente il percorso all'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Specificare il valore per l'azione SAP tramite l'editor espressioni. In questo modo, è possibile usare la stessa azione per diversi tipi di messaggio.

      Per altre informazioni sulle operazioni IDoc, consultare [Schemi di messaggi per operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   2. Fare clic all'interno della casella **Messaggio di input** in modo che venga visualizzato l'elenco di contenuti dinamici. 
   In tale elenco, sotto **Alla ricezione di una richiesta HTTP** selezionare il campo **Corpo**. 

      Questo passaggio include il contenuto del corpo del trigger della richiesta HTTP e invia questo output al server SAP.

      ![Selezionare il campo "Corpo"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Al termine, l'azione SAP avrà un aspetto simile a quello in questo esempio:

      ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>Aggiungere l'azione di risposta HTTP

Ora aggiungere un'azione di risposta al flusso di lavoro dell'app logica e includere l'output dall'azione SAP. In questo modo, l'app per la logica restituisce i risultati dal server SAP per il richiedente originale. 

1. In Progettazione app per la logica, sotto l'azione SAP, scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

2. Nella casella di ricerca digitare "risposta" come filtro. Nell'elenco di azioni selezionare questa azione: **Richiesta - Risposta**

3. Fare clic all'interno della casella **Corpo** casella in modo che venga visualizzato l'elenco di contenuto dinamico. In tale elenco, sotto **Send to SAP** (Invia a SAP), selezionare il campo **Corpo**. 

   ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Salvare l'app per la logica. 

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Se l'app per la logica non è ancora abilitata, scegliere **Panoramica** nel menu dell'app per la logica. Nella barra degli strumenti scegliere **Abilita**. 

2. Nella barra degli strumenti di Progettazione app per la logica selezionare **Esegui**. Questo passaggio avvia manualmente l'app per la logica.

3. Attivare l'app per la logica inviando una richiesta HTTP POST all'URL nel trigger della richiesta HTTP e includere il contenuto del messaggio nella richiesta. Per inviare la richiesta, è possibile usare uno strumento, ad esempio [Postman](https://www.getpostman.com/apps). 

   In questo articolo, la richiesta invia un file IDoc, che deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si sta usando, ad esempio: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Dopo aver inviato la richiesta HTTP, attendere la risposta dall'app per la logica.

   > [!NOTE]
   > L'app per la logica può andare in timeout se tutti i passaggi necessari per la risposta non terminano entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md). Se si verifica questa condizione, le richieste potrebbero venire bloccate. Per diagnosticare i problemi, vedere le informazioni su come [controllare e monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Complimenti, è stata creata un'app per la logica che può comunicare con il server SAP. Dopo aver impostato una connessione a SAP per l'app per la logica, è possibile esplorare altre azioni SAP disponibili, ad esempio BAPI e RFC.

## <a name="receive-from-sap"></a>Ricevere da SAP

Questo esempio usa un'app per la logica che si attiva quando si riceve un messaggio da un sistema SAP. 

### <a name="add-sap-trigger"></a>Aggiungere trigger SAP

1. Nel portale di Azure creare un'app per la logica vuota, che apre la Progettazione app per la logica. 

2. Nella casella di ricerca, immettere "sap" come filtro. Nell'elenco di trigger selezionare questo trigger: **When a message is received from SAP** (Quando si riceve un messaggio da SAP)

   ![Aggiungere trigger SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   In alternativa, è possibile passare alla scheda Enterprise e selezionare il trigger

   ![Aggiungere trigger SAP dalla scheda ent](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. Se vengono richiesti i dettagli della connessione, creare ora la connessione SAP. In caso contrario, se la connessione esiste già, continuare con il passaggio successivo per impostare l'azione SAP. 

   **Creare una connessione SAP locale**

   1. Specificare le informazioni di connessione per il server SAP. 
   Per la proprietà **Gateway dati**, selezionare il gateway dati creato nel portale di Azure per l'installazione del gateway.

      Se la proprietà **Tipo di accesso** è impostata su **Server applicazioni**, sono necessarie le seguenti proprietà (in genere facoltative):

      ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se la proprietà **Tipo di accesso** è impostata su **Gruppo**, sono necessarie le seguenti proprietà (in genere facoltative):

      ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. Specificare i parametri necessari in base alla configurazione del sistema SAP. 

   È possibile anche specificare una o più azioni di SAP. 
   Questo elenco di azioni consente di specificare i messaggi che riceve il trigger dal server SAP tramite il gateway dati. 
   Un elenco vuoto specifica che il trigger riceve tutti i messaggi. 
   Se l'elenco contiene più di un messaggio, il trigger riceve solo i messaggi specificati nell'elenco. Tutti gli altri messaggi inviati dal server SAP vengono rifiutati dal gateway.

   È possibile selezionare un'azione SAP dalla selezione file:

   ![Selezionare l'azione SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   In alternativa, è possibile specificare manualmente l'azione:

   ![Immettere manualmente l'azione SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Di seguito un esempio che illustra come viene visualizzata l'azione quando si configura il trigger per ricevere più messaggi.

   ![Esempio di trigger](media/logic-apps-using-sap-connector/example-trigger.png)  

   Per altre informazioni sull'azione SAP, consultare [Schemi di messaggi per operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

5. Salvare l'app per la logica in modo da poter iniziare a ricevere messaggi dal sistema SAP.
Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

App per la logica è ora pronta per ricevere messaggi dal sistema SAP. 

> [!NOTE]
> Il trigger SAP non è un trigger di poll, ma un trigger basato su webhook. Il trigger viene chiamato dal gateway solo quando esiste un messaggio, pertanto non è necessario alcun polling. 

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Per attivare l'app per la logica, inviare un messaggio dal sistema SAP.

2. Dal menu dell'app per la logica, scegliere **Panoramica** ed esaminare **Cronologia di esecuzione** per eventuali nuove esecuzioni dell'app per la logica. 

3. Aprire l'esecuzione più recente, che mostra il messaggio inviato dal sistema SAP nella sezione output di trigger.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generare gli schemi per gli artefatti in SAP

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. L'azione SAP invia una richiesta a un sistema SAP per generare gli schemi per specifici Intermediate Document (IDoc) e BAPI. Gli schemi che ritornano nella risposta vengono caricati in un Account di integrazione tramite il connettore Azure Resource Manager.

### <a name="add-http-request-trigger"></a>Aggiungere un trigger della richiesta HTTP

1. Nel portale di Azure creare un'app per la logica vuota, che apre la Progettazione app per la logica. 

2. Nella casella di ricerca, digitare "richiesta http" come filtro. Nell'elenco di trigger selezionare questo trigger: **Richiesta - Alla ricezione di una richiesta HTTP**

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Salvare l'app per la logica per poter generare un URL endpoint per l'app per la logica.
Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Aggiungere l'azione SAP per generare schemi

1. In Progettazione app per la logica, sotto il trigger, scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

   ![Aggiungere un'azione](./media/logic-apps-using-sap-connector/add-action.png) 

2. Nella casella di ricerca, immettere "sap" come filtro. Nell'elenco di azioni selezionare questa azione: **Genera schemi**
  
   ![Selezionare l'azione di invio a SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   In alternativa, scegliere la scheda **Enterprise** e selezionare l'azione SAP.

   ![Selezionare l'azione di invio a SAP dalla scheda Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. Se vengono richiesti i dettagli della connessione, creare ora la connessione SAP. In caso contrario, se la connessione esiste già, continuare con il passaggio successivo per impostare l'azione SAP. 

   **Creare una connessione SAP locale**

   1. Specificare le informazioni di connessione per il server SAP. 
   Per la proprietà **Gateway dati**, selezionare il gateway dati creato nel portale di Azure per l'installazione del gateway.

      Se la proprietà **Tipo di accesso** è impostata su **Server applicazioni**, sono necessarie le seguenti proprietà (in genere facoltative):

      ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      Se la proprietà **Tipo di accesso** è impostata su **Gruppo**, sono necessarie le seguenti proprietà (in genere facoltative):
   
      ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Al termine dell'operazione, scegliere **Crea**. Le App per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente.

4. Specificare il percorso dell'artefatto per cui si desidera generare lo schema.

   È possibile selezionare l'azione SAP dalla selezione file:

   ![Selezionare l'azione SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   In alternativa, è possibile immettere manualmente l'azione:

   ![Immettere manualmente l'azione SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   Per generare gli schemi per più artefatti, specificare i dettagli dell'azione SAP per ogni artefatto, ad esempio:

   ![Selezionare Aggiungi nuovo elemento](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![Mostrare due elementi](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   Per altre informazioni sull'azione SAP, vedere [Schemi di messaggi per operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Nella barra degli strumenti della finestra di progettazione, scegliere **Eseguire** per attivare l'esecuzione per l'app per la logica.

2. Aprire l'esecuzione e controllare gli output per l'azione **Genera schema**. 

   Gli output mostrano gli schemi generati per l'elenco specificato di messaggi.

### <a name="upload-schemas-to-integration-account"></a>Caricare gli schemi nell'account di integrazione

Facoltativamente, è possibile scaricare o archiviare gli schemi generati nei repository, quali blob, risorsa di archiviazione o account di integrazione. Gli account di integrazione offrono un'esperienza all'avanguardia con altre azioni XML, quindi l'esempio illustra come caricare gli schemi in un account di integrazione per la stessa app per la logica usando il connettore Azure Resource Manager.

1. Nella finestra di progettazione App per la logica, in trigger, scegliere **Nuovo passaggio** > **Aggiungi un'azione**. Nella casella di ricerca immettere "resource manager" come filtro. Selezionare questa azione: **Create or update a resource** (Crea o aggiorna una risorsa)

   ![Selezionare l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/select-arm-action.png) 

2. Immettere i dettagli, tra cui la sottoscrizione di Azure, il gruppo di risorse di Azure e l'account di integrazione. Per gli altri campi, seguire l'esempio riportato di seguito.

   ![Immettere i dettagli per l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/arm-action.png)

   L'azione SAP **Generare schemi** genera schemi come una raccolta, in modo che la finestra di progettazione aggiunga automaticamente un ciclo **For each** all'azione. 
   Di seguito è riportato un esempio che illustra come viene visualizzata questa azione:

   ![Azione di Azure Resource Manager con ciclo "for each"](media/logic-apps-using-sap-connector/arm-action-foreach.png)  

   > [!NOTE]
   > Gli schemi usano il formato con codifica base64. Per caricare gli schemi in un account di integrazione, devono essere decodificati usando la funzione `base64ToString()`. Di seguito è riportato un esempio che mostra il codice per l'elemento `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

3. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Sulla barra degli strumenti della finestra di progettazione, scegliere **Esegui** per attivare manualmente l'app per la logica.

2. Dopo una corretta esecuzione, passare all'account di integrazione e verificare che esistano gli schemi generati.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

Di seguito sono riportati i problemi attualmente noti e le limitazioni per il connettore SAP:

* Il trigger SAP non supporta la ricezione di IDOC batch da SAP. Questa azione potrebbe causare errori di connessione RFC tra il sistema SAP e il gateway dati.

* Il trigger SAP non supporta i cluster di gateway dati. In alcuni casi di failover, il nodo del gateway dati che comunica con il sistema SAP potrebbe essere diverso dal nodo attivo, causando un comportamento imprevisto. Per gli scenari di invio, sono supportati i cluster di gateway dati.

* Negli scenari di ricezione, non è supportata la restituzione di una risposta non Null. Un'app per la logica con un trigger e un'azione di risposta implica un comportamento imprevisto. 

* Solo una singola chiamata o messaggio Inviare a SAP funziona con tRFC. Il criterio di commit Business Application Programming Interface (BAPI), ad esempio effettuare più chiamate tRFC nella stessa sessione, non è supportato.

* RFC con allegati non sono supportate per le azioni di schemi Inviare a SAP e Generare.

* Il connettore SAP attualmente non supporta le stringhe di route SAP. Il gateway dati locale deve esistere nella stessa rete LAN del sistema SAP a cui si desidera connettersi.

* La conversione di valori assenti (Null), vuoti, minimi e massimi per i campi DATS e TIMS SAP è soggetta a modifiche negli aggiornamenti successivi per il gateway dati locale.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ai sistemi locali](../logic-apps/logic-apps-gateway-connection.md) dalle app per la logica
* Informazioni su come convalidare, trasformare e altre operazioni messaggi con [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
