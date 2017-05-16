---
title: Connessione a un sistema SAP locale nelle App per la logica di Azure | Microsoft Docs
description: Connessione a un sistema SAP locale nel flusso di lavoro delle app per la logica attraverso il gateway dati locale
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc; LADocs
translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 89cd987cee841ea24dce85c0249e0eb3489c8d90
ms.lasthandoff: 05/03/2017

---

# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>Connessione a un sistema SAP locale dalle app per la logica con il connettore SAP 

Il gateway dati locale consente di gestire i dati e accedere in modo sicuro alle risorse presenti in locale. Questo argomento illustra come collegare le app per la logica a un sistema SAP locale. In questo esempio, l'app per la logica richiede un IDOC su HTTP e invia la risposta.    

> [!NOTE]
> Limitazioni correnti: 
> - L'app per la logica va in timeout se tutti i passaggi necessari per la risposta non terminano entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md). In questo scenario, è possibile che le richieste vengano bloccate. 
> - Il selettore file non consente di visualizzare tutti i campi disponibili. In questo scenario, è possibile aggiungere manualmente i percorsi.

## <a name="prerequisites"></a>Prerequisiti

- Installare e configurare il [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) più recente, versione 1.15.6150.1 o superiore. L'articolo sulla [connessione al gateway dati locale in un'app per la logica](http://aka.ms/logicapps-gateway) elenca i passaggi da seguire. Prima di procedere, è necessario installare il gateway in un computer locale.

- Scaricare e installare la libreria client SAP più recente nello stesso computer in cui è stato installato il gateway dati. È possibile usare una delle versioni SAP seguenti: 
    - Server SAP
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode con EHP 4.0
        - SAP ECC 6.0 con EHP 7.0 e tutte le precedenti versioni EHP
 
    - Client SAP
        - SAP Connettore .NET (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>Aggiungere trigger e azioni per la connessione al sistema SAP

Il connettore SAP ha azioni, ma non trigger. Di conseguenza, è necessario usare un altro trigger all'inizio del flusso di lavoro. 

1. Aggiungere il trigger di richiesta/risposta e quindi selezionare **Nuovo passaggio**.

2. Selezionare **Aggiungi un'azione**, quindi selezionare il connettore SAP digitando `SAP` nel campo di ricerca:    

     ![Selezionare il server applicazioni o il server di messaggistica SAP](media/logic-apps-using-sap-connector/sap-action.png)

3. Selezionare il [**server applicazioni SAP**](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o il [**server di messaggistica SAP**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), in base alla configurazione SAP. Se non è già disponibile una connessione, verrà richiesto di crearne una.

   1. Selezionare **Connect via on-premises data gateway** (Connessione tramite gateway dati locale) e immettere i dettagli del sistema SAP:   

       ![Aggiungere la stringa di connessione a SAP](media/logic-apps-using-sap-connector/picture2.png)  

   2. In **Gateway**, selezionare un gateway esistente o per installare un nuovo gateway, selezionare **Installa Gateway**.

        ![Installare un nuovo gateway](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. Dopo aver inserito tutti i dettagli, selezionare **Crea**. 
   Le app per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente.

4. Inserire un nome per la connessione SAP.

5. Sono ora disponibili le diverse opzioni SAP. Per trovare la categoria IDOC, selezionarla dall'elenco. oppure digitare manualmente il percorso e selezionare la risposta HTTP nel campo **corpo**:

     ![Azione SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Aggiungere l'azione per la creazione di una **risposta HTTP**. Il messaggio di risposta deve derivare dall'output SAP.

7. Salvare l'app per la logica. Testarla inviando un IDOC tramite l'URL del trigger HTTP. Dopo l'invio dell'IDOC, attendere la risposta dall'app per la logica:   

     > [!TIP]
     > Scoprire come [monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Ora che il connettore SAP è stato aggiunto all'app per la logica, iniziare a esplorare altre funzionalità:

- BAPI
- RFC

## <a name="get-help"></a>Ottenere aiuto

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti di App per la logica di Azure, vedere il [forum su App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento delle App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come convalidare, trasformare e altre funzioni simili a BizTalk di [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- [Connettersi ai dati locali](../logic-apps/logic-apps-gateway-connection.md) dalle app per la logica

