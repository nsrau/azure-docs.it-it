---
title: Connettersi a sistemi SAP con le App per la logica di Azure | Microsoft Docs
description: Come accedere e gestire le risorse SAP grazie all'automazione dei flussi di lavoro con le App per la logica di Azure
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60847403"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Connettersi a sistemi SAP con App per la logica di Azure

> [!NOTE]
> Questo connettore SAP è pianificato per la deprecazione. Usare o eseguire la migrazione per la [connettore SAP più recente e più avanzato](./logic-apps-using-sap-connector.md). 

Questo articolo illustra come è possibile accedere alle risorse SAP all'interno di un'app per la logica mediante i connettori del server applicazioni SAP e del server di messaggistica SAP. In questo modo, è possibile automatizzare le attività, i processi e i flussi di lavoro che gestiscono i dati e le risorse SAP mediante la creazione di app per la logica.

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. L'app per la logica invia un documento intermedio (IDoc) a un server SAP e restituisce una risposta al richiedente che ha chiamato l'app per la logica.
I connettori SAP correnti presentano azioni, ma non trigger, pertanto in questo esempio viene usato il [trigger richiesta HTTP](../connectors/connectors-native-reqres.md) come primo passaggio nel flusso di lavoro dell'app per la logica. Per informazioni tecniche specifiche del connettore SAP, vedere gli articoli di riferimento: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Connettore del server applicazioni SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Connettore del server di messaggistica SAP</a>

Se non si dispone ancora di una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">registrarsi per creare un account Azure gratuito</a>.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questo articolo, sono necessari questi elementi:

* l'app per la logica da cui si desidera accedere al sistema SAP e un trigger che avvia il flusso di lavoro dell'app per la logica. I connettori SAP attualmente consentono solo di eseguire azioni. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Il <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">server applicazioni SAP</a> o il <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">server di messaggistica SAP</a>

* Scaricare e installare la versione più recente del [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) in qualsiasi computer locale. Prima di proseguire, assicurarsi di configurare il gateway nel portale di Azure. Il gateway consente di accedere in modo sicuro ai dati e alle risorse locali. Per altre informazioni, vedere [Installare il gateway dati locale per le App per la logica di Azure](../logic-apps/logic-apps-gateway-install.md).

* Scaricare e installare la libreria client SAP più recente, che è attualmente <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">Connettore SAP (NCo) 3.0.20.0 per Microsoft .NET Framework 4.0 e Windows a 64 bit (x64)</a>, nello stesso computer del gateway dati locale. Installare questa versione o versioni successive per i motivi seguenti:

  * Le versioni precedenti di NCo SAP possono diventare deadlock quando vengono inviati più messaggi IDoc contemporaneamente. 
  Questa condizione blocca tutti i messaggi successivi che vengono inviati alla destinazione SAP, causando il timeout dei messaggi.

  * Il gateway dati locale viene eseguito solo su sistemi a 64 bit. 
  In caso contrario, viene visualizzato l'errore "immagine non valida" perché il servizio host del gateway dati non supporta gli assembly a 32 bit.

  * Il servizio host del gateway dati e l'adattatore SAP Microsoft usano .NET Framework 4.5. SAP NCo per .NET Framework 4.0 funziona con processi che usano runtime .NET dalla versione 4.0 alla 4.7.1. 
  NCo SAP per .NET Framework 2.0 funziona con processi che usano il runtime .NET da 2.0 a 3.5 e non funziona più con il gateway dati locale più recente.

* Contenuto del messaggio che è possibile inviare al server SAP, ad esempio un file di esempio IDoc. Il contenuto deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si desidera usare.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Aggiungere un trigger della richiesta HTTP

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

In questo esempio, viene creata un'app per la logica con un endpoint in Azure in modo che sia possibile inviare *richieste HTTP POST* all'app per la logica. Quando l'app per la logica riceve le richieste HTTP, il trigger viene attivato ed esegue il passaggio successivo nel flusso di lavoro.

1. Nel portale di Azure creare un'app per la logica vuota, che apre la Progettazione app per la logica. 

2. Nella casella di ricerca, digitare "richiesta http" come filtro. Nell'elenco di trigger selezionare questo trigger: **Richiesta - Alla ricezione di una richiesta HTTP**

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Salvare l'app per la logica per poter generare un URL endpoint per l'app per la logica.
Nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Aggiungere l'azione SAP

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. Se non è ancora stato aggiunto un trigger all'app per la logica e si desidera seguire questo esempio, [aggiungere il trigger descritto in questa sezione](#add-trigger).

1. In Progettazione app per la logica, sotto il trigger, scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

   ![Aggiungere un'azione](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. Nella casella di ricerca immettere "server sap" come filtro. Nell'elenco delle azioni scegliere l'azione per il server SAP: 

   * **Server applicazioni SAP - Send to SAP**(Invia a SAP)
   * **Server di messaggistica SAP - Send to SAP**(Invia a SAP)

   Questo esempio Usa questa azione: **Server applicazioni SAP - Send to SAP**(Invia a SAP)

   ![Selezionare il "Server applicazioni SAP" o "Server di messaggistica SAP"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Se vengono richiesti i dettagli della connessione, creare ora la connessione SAP. In caso contrario, se la connessione esiste già, continuare con il passaggio successivo per impostare l'azione SAP. 

   **Creare una connessione SAP locale**

   1. In **Gateway** selezionare **Connetti tramite gateway dati locale**, in modo che vengano visualizzate le proprietà della connessione locale.

   2. Specificare le informazioni di connessione per il server SAP. 
   Per la proprietà **gateway**, selezionare il gateway dati creato nel portale di Azure per l'installazione del gateway, ad esempio:

      **Server applicazioni SAP**

      ![Creare una connessione al server applicazioni SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Server di messaggistica SAP**

      ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Al termine dell'operazione, scegliere **Crea**.

      Le App per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente.

4. Ora individuare e selezionare un'azione dal server SAP. 

   1. Nella casella **Azione SAP**, scegliere l'icona cartella. 
   Dall'elenco delle cartelle, individuare e selezionare l'azione che si desidera usare. 

      In q esempio viene selezionata la categoria **IDOC** per l'azione IDoc. 

      ![Trovare e selezionare l'azione IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Se non è possibile trovare l'azione desiderata, è possibile immettere manualmente un percorso, ad esempio:

      ![Specificare manualmente il percorso all'azione IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Per altre informazioni sulle operazioni IDoc, vedere [Schemi di messaggi per operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Fare clic all'interno della casella **Messaggio di input** in modo che venga visualizzato l'elenco di contenuti dinamici. 
   In tale elenco, sotto **When a HTTP request is received** (Alla ricezione di una richiesta HTTP), selezionare il campo **Corpo**. 

      Questo passaggio include il contenuto del corpo del trigger della richiesta HTTP e invia questo output al server SAP.

      ![Selezionare il campo "Corpo"](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Al termine, l'azione SAP avrà un aspetto simile a quello in questo esempio:

      ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Aggiungere l'azione di risposta HTTP

Ora aggiungere un'azione di risposta al flusso di lavoro dell'app logica e includere l'output dall'azione SAP. In questo modo, l'app per la logica restituisce i risultati dal server SAP per il richiedente originale. 

1. In Progettazione app per la logica, sotto l'azione SAP, scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

2. Nella casella di ricerca digitare "risposta" come filtro. Nell'elenco di azioni selezionare questa azione: **Richiesta - Risposta**

3. Fare clic all'interno della casella **Corpo** casella in modo che venga visualizzato l'elenco di contenuto dinamico. In tale elenco, sotto **Send to SAP** (Invia a SAP), selezionare il campo **Corpo**. 

   ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Salvare l'app per la logica. 

## <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Se l'app per la logica non è ancora abilitata, scegliere **Panoramica** nel menu dell'app per la logica. Nella barra degli strumenti scegliere **Abilita**. 

2. Nella barra degli strumenti di Progettazione app per la logica selezionare **Esegui**. Questo passaggio avvia manualmente l'app per la logica.

3. Attivare l'app per la logica inviando una richiesta HTTP POST all'URL nel trigger della richiesta HTTP e includere il contenuto del messaggio nella richiesta. Per inviare la richiesta, è possibile usare uno strumento, ad esempio [Postman](https://www.getpostman.com/apps). 

   In questo articolo, la richiesta invia un file IDoc, che deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si sta usando, ad esempio: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Dopo aver inviato la richiesta HTTP, attendere la risposta dall'app per la logica.

> [!NOTE]
> L'app per la logica può andare in timeout se tutti i passaggi necessari per la risposta non terminano entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md). Se si verifica questa condizione, le richieste potrebbero venire bloccate. Per diagnosticare i problemi, vedere le informazioni su come [controllare e monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Complimenti, è stata creata un'app per la logica che può comunicare con il server SAP. Dopo aver impostato una connessione a SAP per l'app per la logica, è possibile esplorare altre azioni SAP disponibili, ad esempio BAPI e RFC.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche dettagliate sul connettore, come descritto dai file Swagger dei connettori, vedere gli articoli di riferimento: 

* [Server applicazioni SAP](/connectors/sapapplicationserver/)
* [Server di messaggistica SAP](/connectors/sapmessageserver/)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ai sistemi locali](../logic-apps/logic-apps-gateway-connection.md) dalle app per la logica
* Informazioni su come convalidare, trasformare e altre operazioni messaggi con [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
