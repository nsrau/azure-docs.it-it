---
title: Connettersi a un server FTP - App per la logica di Azure | Microsoft Docs
description: Creare, monitorare e gestire i file in un server FTP con App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: a14f045193c01b8c26019314ddde4c2116d8bad6
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232818"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Creare, monitorare e gestire i file FTP usando App per la logica di Azure

Con App per la logica di Azure e il connettore FTP è possibile creare attività automatizzate e flussi di lavoro che creano, monitorano, inviano e ricevono file attraverso il proprio account in un server FTP, oltre a eseguire altre azioni come:

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, aggiornare, creare elenchi ed eliminare file.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

È possibile usare i trigger per ottenere risposte dal server FTP e rendere l'output disponibile per altre azioni. È possibile usare azioni nelle app per la logica per eseguire attività con i file sul server FTP. Si può anche fare in modo che altre azioni usino l'output delle azioni FTP. Ad esempio, se si recuperano regolarmente file dal server FTP, è possibile inviare messaggi di posta elettronica riguardanti tali file e il relativo contenuto usando il connettore Outlook di Office 365 o Outlook.com. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Il connettore FTP supporta solo file con dimensioni massime di 50 MB, a meno che non si usi la [divisione in blocchi per gestire i messaggi di grandi dimensioni](../logic-apps/logic-apps-handle-large-messages.md). 
>
> Inoltre, il connettore FTP supporta solo FTP esplicito su SSL (FTPS) e non è compatibile con FTPS implicito. 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Credenziali dell'account e indirizzo del server host FTP

  Il connettore FTP richiede che il server FTP sia accessibile da Internet e sia configurato per operare in modalità *passiva*. Le credenziali autorizzano l'app per la logica a creare una connessione e ad accedere all'account FTP.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account FPT. Per iniziare con un trigger FTP, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione FTP, avviare l'app per la logica con un altro trigger, ad esempio il trigger **Ricorrenza**.

## <a name="connect-to-ftp"></a>Connettersi a FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere "ftp" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato. 

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione scegliere **Nuovo passaggio** e quindi selezionare **Aggiungi un'azione**. 
   Nella casella di ricerca immettere "ftp" come filtro. 
   Nell'elenco delle azioni selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Specificare i dettagli necessari per la connessione, quindi scegliere **Creare**.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="examples"></a>Esempi

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Trigger FTP: Quando viene aggiunto o modificato un file

Questo trigger avvia un flusso di lavoro dell'app per la logica quando rileva che un file è stato aggiunto o modificato in un server FTP. Ad esempio, è possibile aggiungere una condizione che controlla il contenuto del file e decide se leggerlo, a seconda che il contenuto soddisfi una condizione specificata. Infine, è possibile aggiungere un'azione che legga il contenuto del file e inserisca tale contenuto in una cartella nel server SFTP. 

**Esempio per le organizzazioni**: si può usare questo trigger per monitorare una cartella FTP per nuovi file che rappresentano ordini dei clienti. È quindi possibile usare un'azione FTP come **Ottieni contenuto file** per recuperare il contenuto dell'ordine per elaborarlo ulteriormente e archiviare l'ordine nel database degli ordini.

Un'app per la logica valida e funzionale richiede un trigger e almeno un'azione. Assicurarsi quindi di aggiungere un'azione dopo aver aggiunto un trigger.

Ecco un esempio del trigger **Quando viene aggiunto o modificato un file**

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere "ftp" come filtro. Nell'elenco dei trigger selezionare il trigger **Quando viene aggiunto o modificato un file - FTP**

   ![Trovare e selezionare il trigger FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Specificare i dettagli necessari per la connessione, quindi scegliere **Creare**.

   ![Creare la connessione al server FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Accanto alla casella **Cartella** selezionare l'icona della cartella per visualizzare un elenco. Per trovare la cartella che si vuole monitorare per rilevare file nuovi o modificati, selezionare la parentesi uncinata chiusa (**>**), individuare la cartella e quindi selezionarla.

   ![Trovare e selezionare la cartella da monitorare](./media/connectors-create-api-ftp/select-folder.png)  

   La cartella selezionata viene visualizzata nella casella **Cartella**.

   ![Cartella selezionata](./media/connectors-create-api-ftp/selected-folder.png)  

Ora che l'app per la logica ha un trigger, aggiungere le azioni che devono essere eseguite quando l'app trova un file nuovo o modificato. Per questo esempio è possibile aggiungere un'azione FTP che ottiene il contenuto nuovo o aggiornato.

### <a name="ftp-action-get-content"></a>Azione FTP: Ottieni contenuto file

Questa azione recupera il contenuto di un file su un server FTP quando il file viene aggiunto o aggiornato. Ad esempio, è possibile aggiungere il trigger dell'esempio precedente e un'azione che recupera il contenuto del file dopo che il file viene aggiunto o modificato. 

1. Nel trigger o in qualsiasi altra azione scegliere **Nuovo passaggio**. 

1. Nella casella di ricerca immettere "ftp" come filtro. Nell'elenco di azioni selezionare l'azione **Ottieni contenuto file - FTP**

   ![Selezionare un'azione FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Se si ha già una connessione al server FTP e all'account, andare al passaggio successivo. Altrimenti specificare i dettagli necessari per la connessione e quindi scegliere **Crea**. 

   ![Creare la connessione al server FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Dopo l'apertura dell'azione **Ottieni contenuto file**, fare clic all'interno della casella **File** per visualizzare l'elenco di contenuto dinamico. A questo punto è possibile selezionare le proprietà per gli output dei passaggi precedenti. Nell'elenco di contenuto dinamico selezionare la proprietà **Contenuto file**, che corrisponde al contenuto del file aggiunto o aggiornato.  

   ![Trovare e selezionare il file](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   La proprietà **Contenuto file** compare ora nella casella **File**.

   ![Proprietà "Contenuto file" selezionata](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Salvare l'app per la logica. Per testare il flusso di lavoro, aggiungere un file alla cartella FTP che è ora monitorata dall'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/ftpconnector/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)