---
title: Usare il connettore SAP con il gateway dati locale in App per la logica di Azure | Documentazione Microsoft
description: App per la logica consente di eseguire facilmente la connessione a un sistema SAP locale come parte del flusso di lavoro.
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>Introduzione al connettore SAP 

La connettività di cloud ibrido è il fulcro delle app per la logica. Il gateway dati locale consente di gestire i dati e accedere in modo sicuro alle risorse presenti in locale dalle app per la logica. Questo articolo illustra come eseguire la connessione a un sistema SAP locale con uno scenario semplice di richiesta di un IDOC su HTTP e restituzione della risposta.    

 > [!NOTE]
 > Il connettore SAP supporta i sistemi SAP riportati di seguito. Attualmente esistono una limitazione di timeout delle app per la logica che blocca le richieste che superano 90 secondi e una limitazione del numero di campi visualizzabile nella selezione file (i percorsi possono essere aggiunti manualmente)
 >
 >

## <a name="prerequisites"></a>Prerequisiti
- Installare e configurare il [gateway dati locale](https://www.microsoft.com/en-us/download/details.aspx?id=53127) più recente.  

    Installare il gateway dati locale più recente, versione 1.15.6150.1 o successiva, se non è ancora stato fatto. In questo [articolo](http://aka.ms/logicapps-gateway) vengono fornite le istruzioni. Prima di procedere con il resto della procedura, è necessario installare il gateway in un computer locale.

- Scaricare e installare la libreria client SAP più recente nello stesso computer in cui è stato installato il gateway universale.

## <a name="use-sap-connector"></a>Usare il connettore SAP

1. Si creerà un trigger di richiesta HTTP, quindi si selezionerà l'azione del connettore SAP digitando "SAP" nel campo di ricerca.    
 ![Cercare SAP](media/logic-apps-using-sap-connector/picture1.png)

2. Scegliere "SAP" (host applicazioni o host di messaggistica a seconda della configurazione di SAP) e creare una connessione usando il gateway universale.
 - Se non è già disponibile una connessione, verrà richiesto di crearne una.
 - Selezionare l'opzione "Connetti tramite gateway dati locale". Dopo la selezione della casella di controllo verranno visualizzati altri campi.
 - Specificare la stringa del nome della connessione
 - Selezionare il gateway installato nel passaggio precedente oppure selezionare "Installa gateway" per installarne uno nuovo.   
 ![Aggiungere la stringa di connessione a SAP](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > Esistono due diverse connessioni SAP, una per l'[host applicazioni](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) e l'altra per l'[host di messaggistica](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).
  >
  >

3. Dopo avere fornito tutti i dettagli, fare clic su "Crea". Le app per la logica configurano e testano la connessione per verificare che funzioni correttamente. Se la verifica ha esito positivo, verranno visualizzate le opzioni per la scheda precedentemente selezionata. Usare la selezione file per trovare la categoria IDOC corretta oppure digitare manualmente il percorso e selezionare la risposta HTTP nel campo del corpo.    
 ![AZIONE SAP](media/logic-apps-using-sap-connector/picture3.png)

4. Creare una risposta HTTP aggiungendo una nuova azione. Il messaggio di risposta dovrà provenire dall'output SAP.

5. Salvare l'app per la logica e testarla inviando un IDOC tramite l'URL del trigger HTTP.

6. Dopo l'invio dell'IDOC, attendere la risposta dall'app per la logica.   

  > [!TIP]
  > Scoprire come [monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).
  >
  >

7. Al termine, si avrà un'app per la logica funzionante che usa il connettore SAP. È possibile iniziare a conoscere le altre funzionalità che offre:
  - BAPI
  - RFC

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Creare una [connessione locale](../logic-apps/logic-apps-gateway-connection.md) alle app per la logica.


<!--HONumber=Jan17_HO3-->


