---
title: Connettersi a file system locali dalle app per la logica di Azure | Microsoft Docs
description: Connettersi a file system locali dal flusso di lavoro dell'app per la logica tramite il gateway dati locale e il connettore File System
keywords: file system
services: logic-apps
author: derek1ee
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/27/2017
ms.author: LADocs; deli
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: f33e7c58103c57e17e4e273caba1ab9b83f0cd2b
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Connettersi a file system locali dalle app per la logica con il connettore File System

La connettività cloud ibrida è basilare per le app per la logica. In questo modo le app per la logica possono gestire i dati e accedere in modo sicuro alla risorse locali usando il gateway dati locale. Questo articolo descrive come connettersi a un file system locale presentando uno scenario semplice: copiare un file caricato in Dropbox in una condivisione file e successivamente inviare un messaggio di posta elettronica.

## <a name="prerequisites"></a>Prerequisiti

- Installare e configurare il [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) più recente.
- Installare il gateway dati locale più recente, versione 1.15.6150.1 o successiva. [Connessione al gateway dati locale](http://aka.ms/logicapps-gateway) elenca i passaggi. Prima di procedere con il resto della procedura, è necessario installare il gateway in un computer locale.

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Aggiungere trigger e azioni per la connessione al file system

1. Creare un'app per la logica e aggiungere il trigger Dropbox: **Quando viene creato un file** 
2. Nel trigger scegliere **Passaggio successivo** > **Aggiungi un'azione**. 
3. Nella casella di ricerca, immettere `file system` in modo da visualizzare tutte le azioni supportate per il connettore File System.

   ![Cercare il connettore file](media/logic-apps-using-file-connector/search-file-connector.png)

2. Scegliere l'azione **Crea file** e creare una connessione al file system.

   Se non è già disponibile una connessione, verrà richiesto di crearne una.

   1. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale). Vengono visualizzare altre proprietà.
   2. Selezionare la cartella radice per il file system.
      
       > [!NOTE]
       > La cartella radice è la cartella principale che verrà usata per i percorsi relativi di tutte le azioni correlate ai file. È possibile specificare una cartella locale del computer in cui è installato il gateway dati locale oppure la cartella può essere una condivisione di rete a cui il computer ha accesso.

   3. Immettere il nome utente e la password del gateway.
   4. Selezionare il gateway installato in precedenza.

       ![Configurare la connessione](media/logic-apps-using-file-connector/create-file.png)

3. Dopo aver specificato tutti i dettagli, scegliere **Crea**. 

   Le app per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente. 
   Se la connessione è configurata correttamente, vengono visualizzate le opzioni per l'azione selezionata in precedenza. 
   Il connettore del file system sarà pronto all'uso.

4. Specificare che si desidera copiare i file da Dropbox nella cartella radice per la condivisione di file locale.

   ![Azione Crea file](media/logic-apps-using-file-connector/create-file-filled.png)

5. Dopo che l'app per la logica ha copiato il file, aggiungere un'azione di Outlook che invia un messaggio di posta elettronica che informa gli utenti del nuovo file. Immettere i destinatari, il titolo e il corpo del messaggio di posta elettronica. 

   Nel selettore di contenuto dinamico, è possibile scegliere output di dati dal connettore di file per aggiungere altri dettagli al messaggio di posta elettronica.

   ![Azione Invia e-mail](media/logic-apps-using-file-connector/send-email.png)

6. Salvare l'app per la logica. Eseguire il test dell'app caricando un file in Dropbox. Il file dovrebbe essere copiato nella condivisione di file locale e si dovrebbe ricevere il relativo messaggio di posta elettronica di notifica.

   > [!TIP] 
   > Informazioni su come [monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md).

A questo punto, si disporrà di un'app per la logica funzionante che può connettersi al file system locale. Provare a esplorare altre funzionalità del connettore, ad esempio:

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

## <a name="view-the-swagger"></a>Visualizzare il file Swagger
Vedere i [dettagli del file Swagger](/connectors/fileconnector/). 

## <a name="get-help"></a>Ottenere aiuto

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti di App per la logica di Azure, vedere il [forum su App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento delle App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

- [Connettersi ai dati locali](../logic-apps/logic-apps-gateway-connection.md) dalle app per la logica
- Informazioni su [Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)

