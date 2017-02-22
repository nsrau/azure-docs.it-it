---
title: Connettersi a un sistema SAP locale nelle App per la logica di Azure | Microsoft Docs
description: Usare il gateway dati locale per connettersi a un sistema SAP locale nel flusso di lavoro delle app per la logica
services: logic-apps
documentationcenter: 
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 72ac4936c656847fa07f1c1a37d6ddbf4ec1acf4
ms.openlocfilehash: 62b30acf324a5ed6a1b817157c86575d83b4104e


---
# <a name="use-the-sap-connector-in-your-logic-app"></a>Uso del connettore SAP nell'app per la logica 

Il gateway dati locale consente di gestire i dati e accedere in modo sicuro alle risorse presenti in locale. Questo argomento illustra come eseguire la connessione a un sistema SAP locale per effettuare la richiesta di un IDOC su HTTP e restituire la risposta.    

 > [!NOTE]
> Limitazioni correnti:
 > - Interruzione dell'app per la logica se c'è una richiesta che supera 90 secondi. In questo scenario, è possibile che le richieste vengano bloccate. 
 > - Il selettore file non consente di visualizzare tutti i campi disponibili. In questo scenario, è possibile aggiungere manualmente i percorsi.

## <a name="prerequisites"></a>Prerequisiti
- Installare e configurare il [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) più recente, versione 1.15.6150.1 o superiore. L'articolo sulla [connessione al gateway dati locale in un'app per la logica](http://aka.ms/logicapps-gateway) elenca i passaggi da seguire. Prima di procedere, è necessario installare il gateway in un computer locale.

- Scaricare e installare la libreria client SAP più recente nello stesso computer in cui è stato installato il gateway dati. È possibile usare una delle versioni SAP seguenti: 
    - Server SAP
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode con EHP 4.0
        - SAP ECC 6.0 con EHP 7.0 e tutte le precedenti versioni EHP
 
    - Client SAP
        - SAP RFC SDK 7.20 UNICODE
        - SAP Connettore .NET (NCo) 3.0

## <a name="add-the-sap-connector"></a>Aggiungere il connettore SAP

Il connettore SAP ha azioni, ma non include trigger. Di conseguenza, è possibile usare un altro trigger all'inizio del flusso di lavoro. 

1. Aggiungere il trigger di richiesta/risposta e quindi selezionare **Nuovo passaggio**.
2. Selezionare **Aggiungi un'azione**, quindi selezionare il connettore SAP digitando `SAP` nel campo di ricerca:    
 ![Selezionare il server applicazioni o il server di messaggistica SAP](media/logic-apps-using-sap-connector/picture1.png)

3. Selezionare il [server applicazioni](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o il [server di messaggistica](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) **SAP**, in base alla configurazione SAP. Se non è disponibile una connessione, verrà richiesto di crearne una: 
    1. Selezionare **Connect via on-premises data gateway** (Connessione tramite gateway dati locale) e immettere i dettagli del sistema SAP:   
 ![Aggiungere la stringa di connessione a SAP](media/logic-apps-using-sap-connector/picture2.png)  
    2. Selezionare un **Gateway** esistente, oppure, selezionare **Installa gateway** per installare un nuovo gateway:    
 ![Installare un nuovo gateway](media/logic-apps-using-sap-connector/install-gateway.png)
  
    3. Dopo aver inserito tutti i dettagli, selezionare **Crea**. Le app per la logica configurano e testano la connessione per verificare che funzioni correttamente.

4. Inserire un nome per la connessione SAP.

5. Sono ora disponibili le diverse opzioni SAP. Usare il selettore file per individuare la categoria IDOC, oppure digitare manualmente il percorso e selezionare la risposta HTTP nel campo **corpo**:     
 ![AZIONE SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Creare una risposta HTTP mediante l'aggiunta di una nuova azione. Il messaggio di risposta deve derivare dall'output SAP.

7. Salvare l'app per la logica. Testarla inviando un IDOC tramite l'URL del trigger HTTP. Dopo l'invio dell'IDOC, attendere la risposta dall'app per la logica:   

  > [!TIP]
  > Scoprire come [monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Ora che il connettore SAP è stato aggiunto all'app per la logica, iniziare a esplorare altre funzionalità:

  - BAPI
  - RFC

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come convalidare, trasformare e altre funzioni simili a BizTalk di [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Creare una [connessione locale](../logic-apps/logic-apps-gateway-connection.md) alle app per la logica.



<!--HONumber=Feb17_HO1-->


