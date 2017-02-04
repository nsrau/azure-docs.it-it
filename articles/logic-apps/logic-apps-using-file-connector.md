---
title: Usare il connettore file system con il gateway dati locale nelle app per la logica | Microsoft Docs
description: Le app per la logica consentono di connettersi facilmente al file system locale come parte del flusso di lavoro.
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: f2ac0e44f6083d59fc58bc332bfeb5ff39223cce


---
# <a name="use-file-system-connector-with-on-premises-data-gateway"></a>Usare il connettore file system con il gateway dati locale

La connettività di cloud ibrido è il fulcro delle app per la logica. Il gateway dati locale consente di gestire i dati e accedere in modo sicuro alle risorse presenti in locale dalle app per la logica. In questo articolo viene descritto come connettersi a un file system locale presentando uno scenario semplice: copiare un file caricato in Dropbox in una condivisione file e successivamente inviare un'e-mail.

## <a name="prerequisites"></a>Prerequisiti
- Installare e configurare il [gateway dati locale](https://www.microsoft.com/en-us/download/details.aspx?id=53127) più recente.

 Installare il gateway dati locale più recente, versione 1.15.6150.1 o successiva, se non è ancora stato fatto. In questo [articolo](http://aka.ms/logicapps-gateway) vengono fornite le istruzioni. Prima di procedere con il resto della procedura, è necessario installare il gateway in un computer locale.

## <a name="use-file-system-connector"></a>Usare il connettore del file system

1. Creare un trigger "Quando viene creato un file" di Dropbox, per poi dare un'occhiata a tutte le azioni supportate del connettore file con la stessa facilità con cui si digita "File system" nella ricerca.

 ![Cercare il connettore file](media/logic-apps-using-file-connector/search-file-connector.png)

2. Scegliere "Crea file" e creare una connessione per esso.
 - Se non si dispone già di una connessione, verrà chiesto di crearne una.
 - Selezionare la casella di controllo dell'opzione "Connetti tramite il gateway dati locale" per visualizzare altri campi.
 - Specificare la cartella radice, che può essere una cartella locale del computer in cui è installato il gateway dati locale o una condivisione di rete a cui il computer ha accesso.
 - Immettere il nome utente e la password nel gateway.
 - Selezionare il gateway installato nel passaggio precedente.
    
 > [!NOTE]
 > Cartella radice è la cartella padre principale che verrà usata per i percorsi relativi di tutte le azioni correlate ai file.

 ![Configurare la connessione](media/logic-apps-using-file-connector/create-file.png)

3. Dopo avere fornito tutti i dettagli, fare clic su "Crea". Le app per la logica permetteranno di configurare e testare la connessione per assicurarsi che funzioni correttamente. Se tutto viene verificato, si visualizzeranno le opzioni per la scheda selezionata in precedenza e il connettore file sarà pronto all'uso.

4. Prendere il file caricato su Dropbox e copiarlo nella cartella radice della condivisione file in locale.

 ![Azione Crea file](media/logic-apps-using-file-connector/create-file-filled.png)

5. Dopo aver copiato il file, inviare un'e-mail di notifica agli utenti pertinenti. Come per gli altri connettori, l'output delle azioni precedenti sarà disponibile nel selettore "contenuto dinamico".
 - Immettere i destinatari, il titolo e il testo dell'e-mail. Usare il selettore "contenuto dinamico" per selezionare l'output dal connettore file per rendere l'e-mail più completa.

 ![Azione Invia e-mail](media/logic-apps-using-file-connector/send-email.png)

6. Salvare l'app per la logica e testarla caricando un file su Dropbox. Il file copiato dovrebbe apparire nella condivisione di file locale e si dovrebbe ricevere l'e-mail di notifica corrispondente.
 > [!TIP]
 > Scoprire come [monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

7. Al termine della procedura, si dispone finalmente di un app per la logica funzionante che usa il connettore file system. È possibile iniziare a conoscere le altre funzionalità che offre:
    - Crea file
    - Elenca i file nella cartella
    - Aggiungi file
    - Elimina file
    - Recupera contenuto di file
    - Recupera contenuto di file tramite percorso
    - Recupera metadati di file
    - Recupera metadati di file tramite percorso
    - Elenca i file nella cartella radice
    - Aggiorna file

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Creare una [connessione locale](../logic-apps/logic-apps-gateway-connection.md) alle app per la logica.



<!--HONumber=Jan17_HO3-->


