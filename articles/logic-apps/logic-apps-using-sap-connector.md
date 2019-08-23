---
title: Connettersi ai sistemi SAP-app per la logica di Azure
description: Accedere e gestire le risorse SAP automatizzando i flussi di lavoro con le app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/20/2019
tags: connectors
ms.openlocfilehash: 59263f74086f789e46e854ca320455e84dcb42c1
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907548"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Connettersi a sistemi SAP con App per la logica di Azure

Questo articolo illustra come è possibile accedere alle risorse SAP locali dall'interno di un'app per la logica usando il connettore SAP. Il connettore funziona con le versioni classiche di SAP, ad esempio i sistemi R/3 e ECC in locale. Il connettore Abilita anche l'integrazione con i sistemi SAP più recenti di SAP basati su HANA, ad esempio S/4 HANA, indipendentemente dal fatto che siano ospitati in locale o nel cloud. Il connettore SAP supporta l'integrazione di messaggi o dati da e verso sistemi basati su SAP NetWeaver tramite documenti intermedi (IDoc), Business Application Programming Interface (Bova) o RFC (Remote Function Call).

Il connettore SAP usa la [libreria di SAP .NET Connector (NCO)](https://support.sap.com/en/product/connectors/msnet.html) e fornisce le operazioni o le azioni seguenti:

* **Inviare a SAP**: Inviare IDoc tramite tRFC, chiamare le funzioni di Bova su RFC o chiamare RFC/tRFC nei sistemi SAP.
* **Ricevere da SAP**: Ricevere IDoc su tRFC, chiamare le funzioni di Bova su tRFC o chiamare RFC/tRFC nei sistemi SAP.
* **Generare schemi**: Genera schemi per gli artefatti SAP per IDoc, Bruno o RFC.

Per queste operazioni, il connettore SAP supporta l'autenticazione di base tramite nomi utente e password. Il connettore supporta inoltre le [comunicazioni di rete sicure (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). È possibile usare SNC per l'accesso Single Sign-on (SSO) di SAP NetWeaver o per funzionalità di sicurezza aggiuntive fornite da un prodotto di sicurezza esterno.

Il connettore SAP si integra con i sistemi SAP locali tramite il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Negli scenari di invio, ad esempio, quando un messaggio viene inviato da un'app per la logica a un sistema SAP, il gateway dati funge da client RFC e inoltra le richieste ricevute dall'app per la logica a SAP. Analogamente, in scenari di ricezione, il gateway dati funge da Server RFC che riceve le richieste da SAP e le inoltra all'app per la logica.

Questo articolo spiega come creare esempi di app per la logica che si integrano con SAP, illustrando allo stesso tempo gli scenari di integrazione descritti in precedenza.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questo articolo, sono necessari questi elementi:

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* l'app per la logica da cui si desidera accedere al sistema SAP e un trigger che avvia il flusso di lavoro dell'app per la logica. Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per [la logica di Azure](../logic-apps/logic-apps-overview.md) e Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Il [server applicazioni SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o il [Server dei messaggi SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Scaricare e installare la versione più recente del [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) in qualsiasi computer locale. Prima di proseguire, assicurarsi di configurare il gateway nel portale di Azure. Il gateway consente di accedere in modo sicuro ai dati e alle risorse locali. Per altre informazioni, vedere [installare un gateway dati locale per app per la logica di Azure](../logic-apps/logic-apps-gateway-install.md).

* Se si usa SNC con SSO, verificare che il gateway sia in esecuzione come utente mappato all'utente SAP. Per modificare l'account predefinito, selezionare **Cambia account**e immettere le credenziali utente.

  ![Modificare l'account del gateway](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se si Abilita SNC con un prodotto di sicurezza esterno, copiare i file o la libreria SNC nello stesso computer in cui è installato il gateway. Alcuni esempi di prodotti SNC includono [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

* Scaricare e installare la libreria client SAP più recente, che attualmente è il [connettore SAP (NCo 3,0) per Microsoft .NET 3.0.22.0 compilato con .NET Framework 4,0-Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), nello stesso computer del gateway dati locale. Installare questa versione o versioni successive per i motivi seguenti:

  * Le versioni precedenti di SAP NCo potrebbero diventare deadlock quando più di un messaggio IDoc viene inviato nello stesso momento. Questa condizione blocca tutti i messaggi successivi inviati alla destinazione SAP, causando il timeout dei messaggi.
  
  * Il gateway dati locale viene eseguito solo su sistemi a 64 bit. In caso contrario, viene visualizzato l'errore "immagine non valida" perché il servizio host del gateway dati non supporta gli assembly a 32 bit.
  
  * Il servizio host del gateway dati e l'adattatore SAP Microsoft usano .NET Framework 4.5. SAP NCo per .NET Framework 4.0 funziona con processi che usano runtime .NET dalla versione 4.0 alla 4.7.1. SAP NCo per .NET Framework 2,0 funziona con processi che usano il Runtime .NET 2,0 a 3,5, ma non funzionano più con il gateway dati locale più recente.

* Il contenuto del messaggio che è possibile inviare al server SAP, ad esempio un file IDoc di esempio, deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si vuole usare.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>Inviare a SAP

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. L'app per la logica Invia un IDoc a un server SAP e restituisce una risposta al richiedente che ha chiamato l'app per la logica. 

### <a name="add-an-http-request-trigger"></a>Aggiungere un trigger di richiesta HTTP

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

In questo esempio, viene creata un'app per la logica con un endpoint in Azure in modo che sia possibile inviare *richieste HTTP POST* all'app per la logica. Quando l'app per la logica riceve le richieste HTTP, il trigger viene attivato ed esegue il passaggio successivo nel flusso di lavoro.

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica vuota, che apre la Progettazione app per la logica.

1. Nella casella di ricerca, digitare "richiesta http" come filtro. Dall'elenco **trigger** selezionare **quando viene ricevuta una richiesta http**.

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Salvare ora l'app per la logica in modo che sia possibile generare un URL dell'endpoint per l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Aggiungere un'azione SAP

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. Se non è ancora stato aggiunto un trigger all'app per la logica e si desidera seguire questo esempio, [aggiungere il trigger descritto in questa sezione](#add-trigger).

1. Nella finestra di progettazione dell'app per la logica, sotto il trigger, selezionare **nuovo passaggio**.

   ![Selezionare "nuovo passaggio"](./media/logic-apps-using-sap-connector/add-action.png)

1. Nella casella di ricerca, immettere "sap" come filtro. Dall'elenco delle **azioni** selezionare **Invia messaggio a SAP**.
  
   ![Selezionare l'azione di invio a SAP](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   In alternativa, invece di cercare, scegliere la scheda **Enterprise** e selezionare l'azione SAP.

   ![Selezionare l'azione di invio a SAP dalla scheda Enterprise](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se vengono richiesti i dettagli della connessione, creare ora la connessione SAP. In caso contrario, se la connessione esiste già, continuare con il passaggio successivo per poter configurare l'azione SAP.

   **Creare una connessione SAP locale**

   Specificare le informazioni di connessione per il server SAP. Per la proprietà **Gateway dati**, selezionare il gateway dati creato nel portale di Azure per l'installazione del gateway. Al termine, selezionare **Crea**. App per la logica configura e testa la connessione per assicurarsi che la connessione funzioni correttamente.

   * Se la proprietà **Tipo di accesso** è impostata su **Server applicazioni**, sono necessarie le seguenti proprietà (in genere facoltative):

     ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

   * Se la proprietà **Tipo di accesso** è impostata su **Gruppo**, sono necessarie le seguenti proprietà (in genere facoltative):

     ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Altre informazioni sull' [opzione di tipizzazione sicura](#safe-typing).

1. Ora individuare e selezionare un'azione dal server SAP.

   1. Nella casella **azione SAP** selezionare l'icona della cartella. Dall'elenco file, individuare e selezionare il messaggio SAP che si desidera usare. Per spostarsi nell'elenco, usare le frecce.

      Questo esempio Mostra come selezionare un IDoc con il tipo Orders.

      ![Trovare e selezionare l'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se non è possibile trovare l'azione desiderata, è possibile immettere manualmente un percorso, ad esempio:

      ![Specificare manualmente il percorso all'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Specificare il valore per l' **azione SAP** tramite l'editor di espressioni. In questo modo, è possibile utilizzare la stessa azione per diversi tipi di messaggio.

      Per altre informazioni sulle operazioni IDoc, consultare [Schemi di messaggi per operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Fare clic all'interno della casella **Messaggio di input** in modo che venga visualizzato l'elenco di contenuti dinamici. In tale elenco, sotto **Alla ricezione di una richiesta HTTP** selezionare il campo **Corpo**.

      Questo passaggio include il contenuto del corpo del trigger di richiesta HTTP e invia l'output al server SAP.

      ![Selezionare il campo "Corpo"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Al termine, l'azione SAP ha un aspetto simile all'esempio seguente:

      ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Aggiungere un'azione di risposta HTTP

Ora aggiungere un'azione di risposta al flusso di lavoro dell'app logica e includere l'output dall'azione SAP. In questo modo, l'app per la logica restituisce i risultati dal server SAP per il richiedente originale.

1. Nella finestra di progettazione dell'app per la logica, sotto l'azione SAP, selezionare **nuovo passaggio**.

1. Nella casella di ricerca digitare "risposta" come filtro. Nell'elenco **azioni** selezionare **risposta**.

1. Fare clic all'interno della casella **Corpo** casella in modo che venga visualizzato l'elenco di contenuto dinamico. Da tale elenco, in **Invia messaggio a SAP**selezionare il campo **corpo** .

   ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Salvare l'app per la logica.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Se l'app per la logica non è già abilitata, scegliere **Panoramica**dal menu dell'app per la logica. Sulla barra degli strumenti selezionare **Abilita**.

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Esegui**. Questo passaggio avvia manualmente l'app per la logica.

1. Attivare l'app per la logica inviando una richiesta HTTP POST all'URL nel trigger della richiesta HTTP.
Includere il contenuto del messaggio con la richiesta. Per inviare la richiesta, è possibile usare uno strumento, ad esempio [Postman](https://www.getpostman.com/apps).

   In questo articolo, la richiesta invia un file IDoc, che deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si sta usando, ad esempio:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Dopo aver inviato la richiesta HTTP, attendere la risposta dall'app per la logica.

   > [!NOTE]
   > L'app per la logica può andare in timeout se tutti i passaggi necessari per la risposta non terminano entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md). Se si verifica questa condizione, le richieste potrebbero venire bloccate. Per diagnosticare i problemi, vedere le informazioni su come [controllare e monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

A questo punto è stata creata un'app per la logica in grado di comunicare con il server SAP. Dopo aver impostato una connessione a SAP per l'app per la logica, è possibile esplorare altre azioni SAP disponibili, ad esempio BAPI e RFC.

## <a name="receive-from-sap"></a>Ricevere da SAP

Questo esempio usa un'app per la logica che si attiva quando l'app riceve un messaggio da un sistema SAP.

### <a name="add-an-sap-trigger"></a>Aggiungere un trigger SAP

1. Nel portale di Azure creare un'app per la logica vuota, che apre la Progettazione app per la logica.

1. Nella casella di ricerca, immettere "sap" come filtro. Dall'elenco **trigger** selezionare **quando un messaggio viene ricevuto da SAP**.

   ![Aggiungere trigger SAP](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   In alternativa, è possibile passare alla scheda **Enterprise** e selezionare il trigger:

   ![Aggiungi trigger SAP dalla scheda Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se vengono richiesti i dettagli della connessione, creare ora la connessione SAP. Se la connessione esiste già, continuare con il passaggio successivo per poter configurare l'azione SAP.

   **Creare una connessione SAP locale**

   Specificare le informazioni di connessione per il server SAP. Per la proprietà **Gateway dati**, selezionare il gateway dati creato nel portale di Azure per l'installazione del gateway. Al termine, selezionare **Crea**. App per la logica configura e testa la connessione per assicurarsi che la connessione funzioni correttamente.

   * Se la proprietà **Tipo di accesso** è impostata su **Server applicazioni**, sono necessarie le seguenti proprietà (in genere facoltative):

     ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

   * Se la proprietà **Tipo di accesso** è impostata su **Gruppo**, sono necessarie le seguenti proprietà (in genere facoltative):

     ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

   Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Altre informazioni sull' [opzione di tipizzazione sicura](#safe-typing).

1. Specificare i parametri necessari in base alla configurazione del sistema SAP.

   È possibile anche specificare una o più azioni di SAP. Questo elenco di azioni consente di specificare i messaggi che riceve il trigger dal server SAP tramite il gateway dati. Un elenco vuoto specifica che il trigger riceve tutti i messaggi. Se l'elenco contiene più di un messaggio, il trigger riceve solo i messaggi specificati nell'elenco. Tutti gli altri messaggi inviati dal server SAP vengono rifiutati dal gateway.

   È possibile selezionare un'azione SAP dalla selezione file:

   ![Selezionare l'azione SAP](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   In alternativa, è possibile specificare manualmente l'azione:

   ![Immettere manualmente l'azione SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   Di seguito un esempio che illustra come viene visualizzata l'azione quando si configura il trigger per ricevere più messaggi.

   ![Esempio di trigger](media/logic-apps-using-sap-connector/example-trigger.png)  

   Per altre informazioni sull'azione SAP, consultare [Schemi di messaggi per operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Salvare l'app per la logica in modo da poter iniziare a ricevere messaggi dal sistema SAP.
Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

App per la logica è ora pronta per ricevere messaggi dal sistema SAP.

> [!NOTE]
> Il trigger SAP non è un trigger di polling, ma è invece un trigger basato su webhook. Il trigger viene chiamato dal gateway solo quando esiste un messaggio, pertanto non è necessario alcun polling.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Per attivare l'app per la logica, inviare un messaggio dal sistema SAP.

1. Scegliere **Panoramica**dal menu dell'app per la logica. Esaminare la **cronologia** delle esecuzioni per le nuove esecuzioni per l'app per la logica.

1. Aprire l'esecuzione più recente, che mostra il messaggio inviato dal sistema SAP nella sezione output di trigger.

## <a name="receive-idocs-packets-from-sap"></a>Ricevere pacchetti IDOCs da SAP

È possibile configurare SAP per l' [invio di IDOCs in pacchetti](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), ovvero batch o gruppi di IDOCs. Per ricevere pacchetti IDOC, il connettore SAP e in particolare il trigger non necessita di una configurazione aggiuntiva. Tuttavia, per elaborare ogni elemento in un pacchetto IDOC dopo che il trigger ha ricevuto il pacchetto, sono necessari alcuni passaggi aggiuntivi per suddividere il pacchetto in un singolo IDOCs.

Di seguito è riportato un esempio che illustra come estrarre singoli IDOCs da un pacchetto usando la [ `xpath()` funzione](./workflow-definition-language-functions-reference.md#xpath): 

1. Prima di iniziare, è necessaria un'app per la logica con un trigger SAP. Se non si dispone già di questa app per la logica, seguire i passaggi precedenti in questo argomento per configurare un'app per la [logica con un trigger SAP](#receive-from-sap). 

   Esempio:

   ![Trigger SAP](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Ottenere lo spazio dei nomi radice dal IDOC XML che l'app per la logica riceve da SAP. Per estrarre questo spazio dei nomi dal documento XML, aggiungere un passaggio per la creazione di una variabile di stringa locale e archiviare tale `xpath()` spazio dei nomi utilizzando un'espressione:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Ottieni spazio dei nomi](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Per estrarre un singolo IDOC, aggiungere un passaggio per la creazione di una variabile di matrice e archiviare la raccolta IDOC `xpath()` usando un'altra espressione:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')` 

   ![Ottenere una matrice di elementi](./media/logic-apps-using-sap-connector/get-array.png)

   La variabile di matrice rende disponibili ogni IDOC per l'app per la logica da elaborare singolarmente tramite l'enumerazione della raccolta. In questo esempio, l'app per la logica trasferisce ogni IDOC a un server SFTP usando un ciclo:

   ![Invia IDOC](./media/logic-apps-using-sap-connector/loop-batch.png)

   Ogni IDOC deve includere lo spazio dei nomi radice, che è il motivo per cui il contenuto del file `<Receive></Receive` viene incapsulato all'interno di un elemento insieme allo spazio dei nomi radice prima di inviare il IDOC all'app downstream o al server SFTP in questo caso.

> [!TIP]
> È possibile usare il modello di avvio rapido per questo modello selezionando questo modello nella finestra di progettazione dell'app per la logica quando si crea una nuova app per la logica.
>
> ![Modello batch](./media/logic-apps-using-sap-connector/batch-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generare gli schemi per gli artefatti in SAP

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. L'azione SAP invia una richiesta a un sistema SAP per generare gli schemi per i IDoc e i bruni specificati. Gli schemi che restituiscono nella risposta vengono caricati in un account di integrazione usando il connettore Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Aggiungere un trigger di richiesta HTTP

1. Nel portale di Azure creare un'app per la logica vuota, che apre la Progettazione app per la logica.

1. Nella casella di ricerca, digitare "richiesta http" come filtro. Dall'elenco **trigger** selezionare **quando viene ricevuta una richiesta http**.

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Salvare l'app per la logica per poter generare un URL endpoint per l'app per la logica.
Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Aggiungere un'azione SAP per generare schemi

1. Nella finestra di progettazione dell'app per la logica, sotto il trigger, selezionare **nuovo passaggio**.

   ![Selezionare "nuovo passaggio"](./media/logic-apps-using-sap-connector/add-action.png)

1. Nella casella di ricerca, immettere "sap" come filtro. Nell'elenco **azioni** selezionare **genera schemi**.
  
   ![Selezionare l'azione di invio a SAP](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   In alternativa, è anche possibile scegliere la scheda **Enterprise** e selezionare l'azione SAP.

   ![Selezionare l'azione di invio a SAP dalla scheda Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se vengono richiesti i dettagli della connessione, creare ora la connessione SAP. Se la connessione esiste già, continuare con il passaggio successivo per poter configurare l'azione SAP.

   **Creare una connessione SAP locale**

   1. Specificare le informazioni di connessione per il server SAP. Per la proprietà **Gateway dati**, selezionare il gateway dati creato nel portale di Azure per l'installazione del gateway.

      - Se la proprietà **Tipo di accesso** è impostata su **Server applicazioni**, sono necessarie le seguenti proprietà (in genere facoltative):

        ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - Se la proprietà **Tipo di accesso** è impostata su **Gruppo**, sono necessarie le seguenti proprietà (in genere facoltative):

        ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Altre informazioni sull' [opzione di tipizzazione sicura](#safe-typing).

   1. Al termine, selezionare **Crea**. 
   
      App per la logica configura e testa la connessione per assicurarsi che la connessione funzioni correttamente.

1. Specificare il percorso dell'artefatto per cui si desidera generare lo schema.

   È possibile selezionare l'azione SAP dalla selezione file:

   ![Selezionare l'azione SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   In alternativa, è possibile immettere manualmente l'azione:

   ![Immettere manualmente l'azione SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Per generare gli schemi per più artefatti, specificare i dettagli dell'azione SAP per ogni artefatto, ad esempio:

   ![Selezionare Aggiungi nuovo elemento](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrare due elementi](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Per ulteriori informazioni sull'azione SAP, vedere la pagina relativa agli [schemi di messaggio per le operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Esegui** per attivare un'esecuzione per l'app per la logica.

1. Aprire l'esecuzione e controllare gli output per l'azione **genera schemi** .

   Gli output mostrano gli schemi generati per l'elenco specificato di messaggi.

### <a name="upload-schemas-to-an-integration-account"></a>Caricare gli schemi in un account di integrazione

Facoltativamente, è possibile scaricare o archiviare gli schemi generati nei repository, quali blob, risorsa di archiviazione o account di integrazione. Gli account di integrazione offrono un'esperienza all'avanguardia con altre azioni XML, quindi l'esempio illustra come caricare gli schemi in un account di integrazione per la stessa app per la logica usando il connettore Azure Resource Manager.

1. Nella finestra di progettazione dell'app per la logica, sotto il trigger, selezionare **nuovo passaggio**.

1. Nella casella di ricerca immettere "Gestione risorse" come filtro. Selezionare **Crea o aggiorna una risorsa**.

   ![Selezionare l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Immettere i dettagli per l'azione, tra cui la sottoscrizione di Azure, il gruppo di risorse di Azure e l'account di integrazione. Per aggiungere token SAP ai campi, fare clic all'interno delle caselle per tali campi e selezionare dall'elenco di contenuto dinamico visualizzato.

   1. Aprire l'elenco **Aggiungi nuovo parametro** e selezionare il **percorso** e i campi delle **proprietà** .

   1. Specificare i dettagli per questi nuovi campi, come illustrato in questo esempio.

      ![Immettere i dettagli per l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   L'azione SAP **Generare schemi** genera schemi come una raccolta, in modo che la finestra di progettazione aggiunga automaticamente un ciclo **For each** all'azione. Di seguito è riportato un esempio che illustra come viene visualizzata questa azione:

   ![Azione di Azure Resource Manager con ciclo "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  
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

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Esegui** per attivare manualmente l'app per la logica.

1. Una volta completata l'esecuzione, passare all'account di integrazione e verificare che gli schemi generati esistano.

## <a name="enable-secure-network-communications"></a>Abilitare le comunicazioni di rete sicure

Prima di iniziare, verificare di aver soddisfatto i [prerequisiti](#pre-reqs)elencati in precedenza:

* Il gateway dati locale è installato in un computer che si trova nella stessa rete del sistema SAP.
* Per SSO, il gateway viene eseguito come un utente di cui è stato eseguito il mapping a un utente SAP.
* La libreria SNC che fornisce le funzioni di sicurezza aggiuntive viene installata nello stesso computer del gateway dati. Alcuni esempi includono [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

   Per abilitare SNC per le richieste da e verso il sistema SAP, selezionare la casella di controllo **USA SNC** nella connessione SAP e specificare le proprietà seguenti:

   ![Configurare SAP SNC nella connessione](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Proprietà | DESCRIZIONE |
   |----------| ------------|
   | **Percorso della libreria SNC** | Nome o percorso della libreria SNC relativo al percorso di installazione di NCo o al percorso assoluto. Gli esempi `sapsnc.dll` sono `.\security\sapsnc.dll` o `c:\security\sapsnc.dll`o. |
   | **SNC SSO** | Quando ci si connette tramite SNC, l'identità SNC viene in genere usata per l'autenticazione del chiamante. Un'altra opzione consiste nell'eseguire l'override di in modo che sia possibile usare le informazioni relative all'utente e alla password per l'autenticazione del chiamante, ma la riga è ancora crittografata. |
   | **Nome della SNC** | Nella maggior parte dei casi, questa proprietà può essere omessa. La soluzione SNC installata in genere conosce il proprio nome SNC. Solo per le soluzioni che supportano più identità, potrebbe essere necessario specificare l'identità da usare per questa particolare destinazione o server. |
   | **Nome del partner SNC** | Nome del back-end SNC. |
   | **Qualità della protezione di SNC** | Qualità del servizio da utilizzare per la comunicazione SNC di questa particolare destinazione o server. Il valore predefinito è definito dal sistema back-end. Il valore massimo è definito dal prodotto di sicurezza usato per SNC. |
   |||

   > [!NOTE]
   > Non impostare le variabili di ambiente SNC_LIB e SNC_LIB_64 nel computer in cui sono presenti il gateway dati e la libreria SNC. Se impostate, hanno la precedenza sul valore della libreria SNC passato attraverso il connettore.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Tipizzazione sicura

Per impostazione predefinita, quando si crea la connessione SAP, viene usata la tipizzazione forte per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Se si sceglie la **tipizzazione sicura**, il tipo di file dat e il tipo di Tim in SAP vengono considerati stringhe anziché come equivalenti `xs:time`XML, `xmlns:xs="http://www.w3.org/2001/XMLSchema"` `xs:date` e, dove. La tipizzazione sicura influiscono sul comportamento per la generazione di tutti gli schemi, il messaggio di invio sia per il payload "inviato" sia per la risposta "ricevuto" e il trigger. 

Quando si usa la tipizzazione forte (la tipizzazione**sicura** non è abilitata), lo schema esegue il mapping dei tipi dat e Tim ai tipi XML più semplici:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando si inviano messaggi utilizzando la tipizzazione forte, la risposta dat e Tim è conforme al formato di tipo XML corrispondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando è abilitata la **tipizzazione sicura** , lo schema esegue il mapping dei tipi dat e Tim ai campi stringa XML con solo restrizioni di lunghezza, ad esempio:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Quando i messaggi vengono inviati con la **tipizzazione sicura** abilitata, la risposta dat e Tim è simile a questo esempio:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

Di seguito sono riportati i problemi attualmente noti e le limitazioni per il connettore SAP:

* Il trigger SAP non supporta i cluster di gateway dati. In alcuni casi di failover, il nodo del gateway dati che comunica con il sistema SAP potrebbe essere diverso dal nodo attivo, il che comporta un comportamento imprevisto. Per gli scenari di invio, sono supportati i cluster di gateway dati.

* Il connettore SAP attualmente non supporta le stringhe di route SAP. Il gateway dati locale deve esistere nella stessa rete LAN del sistema SAP a cui si desidera connettersi.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, descritti dalla descrizione del connettore OpenAPI (in precedenza spavalderia), vedere la [pagina di riferimento del connettore](/connectors/sap/).

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ai sistemi locali](../logic-apps/logic-apps-gateway-connection.md) da app per la logica di Azure.
* Informazioni su come convalidare, trasformare e usare altre operazioni dei messaggi con la [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).
* Informazioni su altri [connettori per app](../connectors/apis-list.md)per la logica.
