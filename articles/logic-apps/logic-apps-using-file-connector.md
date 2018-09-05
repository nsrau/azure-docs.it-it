---
title: Connettersi a file system locali - App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro che si collegano ai file system locali con il connettore File System tramite il gateway dati locale in App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 41dd8ad721329c4c4d2761c9e4a37c640251dac3
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125279"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Connettersi a file system locali dalle App per la logica di Azure

Con il connettore File System e le App per la logica di Azure, è possibile creare attività automatizzate e flussi di lavoro che creano e gestiscono i file in una condivisione di file locale, ad esempio:  

- Creare, leggere, accodare, aggiornare ed eliminare file
- Elencare i file in cartelle o in cartelle radice.
- Leggere contenuti e metadati dei file.

Questo articolo illustra come connettersi a un file system locale come descritto in questo scenario di esempio: copiare un file caricato in Dropbox in una condivisione file e quindi inviare un messaggio di posta elettronica. Per connettersi e accedere ai sistemi locali, le app per la logica usano il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Prima di poter connettere le app per la logica a sistemi locali, ad esempio il server del file system, è necessario [installare e configurare un gateway dati locale](../logic-apps/logic-apps-gateway-install.md). In questo modo, è possibile specificare di usare l'installazione del gateway quando si crea la connessione al file system dall'app per la logica.

* Un [account Dropbox](https://www.dropbox.com/) e le credenziali utente

  Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account Dropbox. 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per questo esempio, è necessaria un'app per la logica vuota.

## <a name="add-trigger"></a>Aggiunta di trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Nella casella di ricerca immettere "dropbox" come filtro. Nell'elenco di trigger selezionare questo trigger: **When a file is created** (Quando viene creato un file) 

   ![Selezionare un trigger Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Accedere con le credenziali dell'account Dropbox e autorizzare l'accesso ai dati di Dropbox delle App per la logica di Azure. 

1. Specificare le informazioni richieste per il trigger.

   ![Trigger Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Aggiunta di azioni

1. Nel trigger scegliere **Passaggio successivo**. Nella casella di ricerca immettere "file system" come filtro. Nell'elenco di azioni selezionare l'azione **Create file - File System**

   ![Trovare il connettore File System](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se non è ancora disponibile una connessione al file system, viene chiesto se crearla.

   ![Creare la connessione](media/logic-apps-using-file-connector/file-system-connection.png)

   | Proprietà | Obbligatoria | Valore | DESCRIZIONE | 
   | -------- | -------- | ----- | ----------- | 
   | **Connection Name** (Nome connessione) | Yes | <*nome connessione*> | Il nome da assegnare alla connessione | 
   | **Cartella radice** | Yes | <*root-folder-name*> | La cartella radice del file system, ad esempio una cartella locale nel computer in cui è installato il gateway dati locale o la cartella per una condivisione di rete a cui il computer possa accedere. <p>Ad esempio: `\\PublicShare\\DropboxFiles` <p>La cartella radice è la cartella principale che verrà usata per i percorsi relativi di tutte le azioni correlate ai file. | 
   | **Tipo di autenticazione** | No  | <*auth-type*> | Il tipo di autenticazione usato dal file system, ad esempio **Windows** | 
   | **Nome utente** | Yes | <*domain*>\\<*username*> | Il nome utente per il gateway di dati installato in precedenza | 
   | **Password** | Yes | <*your-password*> | La password per il gateway di dati installato in precedenza | 
   | **gateway** | Yes | <*installed-gateway-name*> | Il nome del gateway installato in precedenza | 
   ||| 

1. Al termine dell'operazione, scegliere **Crea**. 

   Le app per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente. 
   Se la connessione è configurata correttamente, vengono visualizzate le opzioni per l'azione selezionata in precedenza. 

1. Nell'azione **Crea file** fornire i dettagli per copiare i file da Dropbox alla cartella radice nella condivisione file locale. Per aggiungere gli output dai passaggi precedenti, fare clic all'interno delle caselle e selezionare i campi disponibili quando viene visualizzato l'elenco di contenuto dinamico.

   ![Azione Crea file](media/logic-apps-using-file-connector/create-file-filled.png)

1. A questo punto, aggiungere un'azione di Outlook che invii un messaggio di posta elettronica in modo che gli utenti appropriati vengano a conoscenza del nuovo file. Immettere i destinatari, il titolo e il corpo del messaggio di posta elettronica. Ai fini del test è possibile indicare il proprio indirizzo e-mail.

   ![Azione Invia e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Salvare l'app per la logica. Eseguire il test dell'app caricando un file in Dropbox. 

   L'app per la logica deve copiare il file nella condivisione di file locale e inviare ai destinatari un messaggio di posta elettronica relativo al file copiato.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/fileconnector/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Per contribuire al miglioramento di App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica di Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi ai dati locali](../logic-apps/logic-apps-gateway-connection.md) 
* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
