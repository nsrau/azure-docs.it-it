---
title: Connettersi a una cartella del file system locale nelle App per la logica di Azure | Documentazione Microsoft
description: Usare il gateway dati locale per connettersi a un file system locale nel flusso di lavoro delle app per la logica
services: logic-apps
documentationcenter: dev-center-name
author: derek1ee
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 336e3f2bba2fe3bb7abdd6959354349ff61ed6a8
ms.openlocfilehash: 3bbcb5e9c7a4731eb8eb410a94fddb93b2ea8416


---
# <a name="use-the-file-system-connector-with-the-on-premises-data-gateway-in-a-logic-app"></a>Usare il connettore del file system con il gateway dati locale nelle app per la logica

La connettività di cloud ibrido è il fulcro delle app per la logica. Il gateway dati locale consente di gestire i dati e accedere in modo sicuro alle risorse presenti in locale dalle app per la logica. In questo articolo viene descritto come connettersi a un file system locale presentando uno scenario semplice: copiare un file caricato in Dropbox in una condivisione file e successivamente inviare un'e-mail.

## <a name="prerequisites"></a>Prerequisiti
- Installare e configurare il [gateway dati locale](https://www.microsoft.com/en-us/download/details.aspx?id=53127) più recente.
- Installare il gateway dati locale più recente, versione 1.15.6150.1 o successiva. [Connessione al gateway dati locale](http://aka.ms/logicapps-gateway) elenca i passaggi. Prima di procedere con il resto della procedura, è necessario installare il gateway in un computer locale.

## <a name="use-file-system-connector"></a>Usare il connettore del file system

1. Creare un trigger "Quando viene creato un file" di Dropbox, per poi dare un'occhiata a tutte le azioni supportate del connettore file con la stessa facilità con cui si digita "File system" nella ricerca.

 ![Cercare il connettore file](media/logic-apps-using-file-connector/search-file-connector.png)

2. Scegliere "Crea file" e creare una connessione per esso.
 - Se non è già disponibile una connessione, verrà richiesto di crearne una.
 - Selezionare l'opzione Connetti tramite gateway dati locale. Vengono visualizzate ulteriori proprietà.
 - Selezionare la cartella radice. La cartella radice può essere una cartella locale del computer in cui è installato il gateway dati locale o una condivisione di rete a cui il computer ha accesso.
 - Immettere il nome utente e la password nel gateway.
 - Selezionare il gateway installato nel passaggio precedente.
    
 > [!NOTE]
 > La cartella radice è la cartella principale che verrà usata per i percorsi relativi di tutte le azioni correlate ai file.

 ![Configurare la connessione](media/logic-apps-using-file-connector/create-file.png)

3. Dopo avere fornito tutti i dettagli, fare clic su "Crea". Le app per la logica configurano e testano la connessione per verificare che funzioni correttamente. Se tutto viene verificato, si visualizzeranno le opzioni per la scheda selezionata in precedenza. Il connettore del file system sarà pronto all'uso.

4. Prendere il file caricato su Dropbox e copiarlo nella cartella radice della condivisione file in locale.

 ![Azione Crea file](media/logic-apps-using-file-connector/create-file-filled.png)

5. Dopo aver copiato il file, inviare un'e-mail di notifica agli utenti pertinenti. Come per gli altri connettori, l'output delle azioni precedenti sarà disponibile nel selettore "contenuto dinamico".
 - Immettere i destinatari, il titolo e il corpo del messaggio di posta elettronica. Usare il selettore "contenuto dinamico" per selezionare l'output dal connettore file per rendere il messaggio di posta elettronica più completo.

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



<!--HONumber=Jan17_HO5-->


