---
title: Connettersi a file system locali - App per la logica di Azure | Microsoft Docs
description: Connettersi a file system locali da flussi di lavoro di app per la logica tramite il gateway dati locale e il connettore File System
keywords: file system, locale
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
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 7738b3346af49cb8aa811eb17003d1b72b1bbe46
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Connettersi a file system locali dalle app per la logica con il connettore File System

Per gestire i dati e accedere in modo sicuro a risorse locali, le app per la logica possono usare il gateway dati locale. Questo articolo mostra come connettersi a un file system locale tramite questo scenario di esempio di base: copiare un file caricato in Dropbox in una condivisione file e quindi inviare un messaggio di posta elettronica.

## <a name="prerequisites"></a>Prerequisiti

* Scaricare il [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) più recente.

* Installare e configurare il gateway dati locale più recente, versione 1.15.6150.1 o successiva. Per la procedura, vedere [Connettersi a origini dati in locale](http://aka.ms/logicapps-gateway). Prima di procedere con questi passaggi, è necessario installare il gateway in un computer locale.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Aggiungere trigger e azioni per la connessione al file system

1. Creare un'app per la logica vuota. Aggiungere questo trigger come primo passaggio: **Dropbox - Quando viene creato un file** 

2. Nel trigger scegliere **Passaggio successivo** > **Aggiungi un'azione**. 

3. Nella casella di ricerca immettere "file system" come filtro. Quando vengono visualizzate tutte le azioni per il connettore File System, scegliere l'azione **File system - Crea file**. 

   ![Cercare il connettore file](media/logic-apps-using-file-connector/search-file-connector.png)

4. Se non è ancora disponibile una connessione al file system, viene chiesto se crearla. 

5. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale). Quando vengono visualizzate le proprietà di connessione, configurare la connessione come specificato nella tabella.

   ![Configurare la connessione](media/logic-apps-using-file-connector/create-file.png)

   | Impostazione | Descrizione |
   | ------- | ----------- |
   | **Cartella radice** | Specificare la cartella radice per il file system. È possibile specificare una cartella locale del computer in cui è installato il gateway dati locale oppure la cartella può essere una condivisione di rete a cui il computer ha accesso. <p>**Suggerimento:** la cartella radice è la cartella padre principale, usata per i percorsi relativi per tutte le azioni correlate ai file. | 
   | **Tipo di autenticazione** | Tipo di autenticazione usato dal file system | 
   | **Nome utente** | Specificare il nome utente {*dominio*\\*nomeutente*} per il gateway installato in precedenza. | 
   | **Password** | Specificare la password per il gateway installato in precedenza. | 
   | **Gateway** | Selezionare il gateway installato in precedenza. | 
   ||| 

6. Dopo aver specificato tutti i dettagli di connessione, scegliere **Crea**. 

   Le app per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente. 
   Se la connessione è configurata correttamente, vengono visualizzate le opzioni per l'azione selezionata in precedenza. 
   Il connettore del file system sarà pronto all'uso.

7. Configurare l'azione **Crea file** per copiare i file da Dropbox alla cartella radice per la condivisione file locale.

   ![Azione Crea file](media/logic-apps-using-file-connector/create-file-filled.png)

8. Dopo questa azione per la copia del file, aggiungere un'azione di Outlook per l'invio di un messaggio di posta elettronica in modo che gli utenti interessati siano informati del nuovo file. Immettere i destinatari, il titolo e il corpo del messaggio di posta elettronica. 

   Nell'elenco **Contenuto dinamico** è possibile scegliere output di dati dal connettore di file per aggiungere altri dettagli al messaggio di posta elettronica.

   ![Azione Invia e-mail](media/logic-apps-using-file-connector/send-email.png)

9. Salvare l'app per la logica. Eseguire il test dell'app caricando un file in Dropbox. Il file dovrebbe essere copiato nella condivisione di file locale e si dovrebbe ricevere il relativo messaggio di posta elettronica di notifica.

A questo punto, si disporrà di un'app per la logica funzionante che può connettersi al file system locale. 

Provare a esplorare altre funzionalità del connettore, ad esempio:

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

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Per contribuire al miglioramento di App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ai dati locali](../logic-apps/logic-apps-gateway-connection.md) 
* [Monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Integrazione aziendale per scenari B2B](../logic-apps/logic-apps-enterprise-integration-overview.md)
