---
title: Connettersi al server FTP
description: Automatizzare attività e flussi di lavoro che creano, monitorano e gestiscono file in un server FTP usando App per la logica di AzureAutomate tasks and workflows that create, monitor, and manage files on an FTP server by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 94ca609d9dc070e6e2b4dc878ecd8dfaf9331ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648173"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Creare, monitorare e gestire i file FTP usando App per la logica di Azure

Con App per la logica di Azure e il connettore FTP è possibile creare attività automatizzate e flussi di lavoro che creano, monitorano, inviano e ricevono file attraverso il proprio account in un server FTP, oltre a eseguire altre azioni come:

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, aggiornare, creare elenchi ed eliminare file.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

È possibile usare i trigger per ottenere risposte dal server FTP e rendere l'output disponibile per altre azioni. È possibile usare azioni di esecuzione nelle app per la logica per gestire i file sul server FTP. Si può anche fare in modo che altre azioni usino l'output delle azioni FTP. Ad esempio, se si ottengono regolarmente file dal server FTP, è possibile inviare messaggi di posta elettronica riguardanti tali file e il relativo contenuto usando il connettore Outlook di Office 365 o Outlook.com. Se non si ha familiarità con le app per la logica, vedere [Che cos'è App per la logica](../logic-apps/logic-apps-overview.md)di Azure .

## <a name="limitations"></a>Limitazioni

* Il connettore FTP supporta solo FTP esplicito su SSL (FTPS) e non è compatibile con FTPS implicito.

* Per impostazione predefinita, le azioni FTP possono leggere o scrivere file di dimensioni pari o inferiori a *50 MB.* Per gestire file di dimensioni superiori a 50 MB, le azioni FTP supportano la [suddivisione in blocchi dei](../logic-apps/logic-apps-handle-large-messages.md)messaggi. L'azione **Ottieni contenuto file** utilizza in modo implicito la suddivisione in blocchi.

* I trigger FTP non supportano la suddivisione in blocchi. Quando si richiede il contenuto del file, i trigger selezionano solo i file di dimensioni pari o inferiori a 50 MB. Per recuperare file di dimensione superiore a 50 MB, seguire questo modello:

  * Utilizzare un trigger FTP che restituisce le proprietà del file, ad esempio **Quando un file viene aggiunto o modificato (solo proprietà)**.

  * Seguire il trigger con l'azione **FTP Get file content** , che legge il file completo e utilizza in modo implicito la suddivisione in blocchi.

* Se si dispone di un server FTP locale, è consigliabile creare un ambiente del servizio di [integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) o usare [le connessioni ibride](../app-service/app-service-hybrid-connections.md)del servizio app di Azure, che consentono entrambi di accedere alle origini dati locali senza usare un gateway dati locale.

## <a name="how-ftp-triggers-work"></a>Funzionamento dei trigger FTP

I trigger FTP funzionano eseguendo il polling del file system FTP e cercando qualsiasi file modificato dopo l'ultimo sondaggio. Alcuni strumenti consentono di mantenere il timestamp quando i file vengono modificati. In questi casi è necessario disabilitare questa funzionalità per consentire il funzionamento del trigger. Ecco alcune delle impostazioni comuni:

| Client SFTP | Azione |
|-------------|--------|
| Winscp | Vai a **Opzioni** > **Preferenze** > **Trasferisci** > **Modifica** > **Mantieni timestamp** > **Disabilita** |
| FileZilla | Vai a **Trasferisci** > **Conserva timestamp dei file** > trasferiti**Disattiva** |
|||

Quando un trigger rileva un nuovo file, controlla che sia completo e non parzialmente scritto. Ad esempio, un file potrebbe avere delle modifiche in corso nel momento in cui il trigger controlla il file server. Per evitare la restituzione di un file scritto parzialmente, il trigger prende nota del timestamp del file che contiene le modifiche recenti ma non restituisce immediatamente il file. Il trigger restituisce il file solo durante il nuovo polling del server. In alcuni casi, questo comportamento potrebbe causare un ritardo fino a un massimo del doppio dell'intervallo di polling del trigger.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Credenziali dell'account e indirizzo del server host FTP

  Il connettore FTP richiede che il server FTP sia accessibile da Internet e sia configurato per operare in modalità *passiva*. Le credenziali consentono all'app per la logica di creare una connessione e di accedere all'account FTP.

* Informazioni di base sulla [creazione di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account FPT. Per iniziare con un trigger FTP, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione FTP, avviare l'app per la logica con un altro trigger, ad esempio il trigger **Ricorrenza**.

## <a name="connect-to-ftp"></a>Connettersi a FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale](https://portal.azure.com)di Azure e aprire l'app per la logica in Progettazione app per la logica.

1. Per le app per la logica `ftp` vuote, nella casella di ricerca immettere il filtro. Nell'elenco **Trigger** selezionare il trigger desiderato.

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione selezionare **Nuovo passaggio**e quindi **Aggiungi un'azione**. Nella casella di ricerca immettere `ftp` come filtro. Nell'elenco **Azioni** selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Fornire le informazioni di connessione e selezionare **Crea**.

1. Fornire le informazioni per il trigger o l'azione selezionata e continuare a compilare il flusso di lavoro dell'app per la logica.

## <a name="examples"></a>Esempi

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Aggiungi trigger FTP

Il trigger **Quando un file viene aggiunto o modificato (solo proprietà)** avvia un flusso di lavoro dell'app per la logica quando il trigger rileva che un file viene aggiunto o modificato in un server FTP. Ad esempio, è possibile aggiungere una condizione che controlla il contenuto del file e decide se ottenere tale contenuto, a seconda che tale contenuto soddisfi una condizione specificata. Infine, è possibile aggiungere un'azione che ottiene il contenuto del file e inserire tale contenuto in una cartella diversa sul server SFTP.

Ad esempio, è possibile utilizzare questo trigger per monitorare una cartella FTP per i nuovi file che descrivono gli ordini dei clienti. È quindi possibile utilizzare un'azione FTP, ad esempio **Ottenere i metadati** del file per ottenere le proprietà per il nuovo file e quindi utilizzare **Get contenuto del file** per ottenere il contenuto da tale file per un'ulteriore elaborazione e archiviare tale ordine in un database orders.

Di seguito è riportato un esempio che illustra come utilizzare il trigger **Quando un file viene aggiunto o modificato (solo proprietà).**

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica `ftp` vuote, nella casella di ricerca immettere il filtro. Nell'elenco dei trigger selezionare questo trigger: **quando un file viene aggiunto o modificato (solo proprietà)**

   ![Trovare e selezionare il trigger FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Specificare i dettagli necessari per la connessione e quindi selezionare **Crea**.

   Per impostazione predefinita, questo connettore trasferisce i file in formato testo. Per trasferire i file in formato binario, ad esempio dove e quando viene usata la codifica, selezionare **Binary Transport** (Trasporto binario).

   ![Crea connessione al server FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Nella casella **Cartella** selezionare l'icona della cartella in modo che venga visualizzato un elenco. Per trovare la cartella che si desidera monitorare per i**>** file nuovi o modificati, selezionare la freccia dell'angolo destro ( ), individuare tale cartella e quindi selezionare la cartella.

   ![Trovare e selezionare la cartella da monitorare](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   La cartella selezionata viene visualizzata nella casella **Cartella**.

   ![La cartella selezionata viene visualizzata nella proprietà "Cartella"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

Ora che l'app per la logica ha un trigger, aggiungere le azioni che devono essere eseguite quando l'app trova un file nuovo o modificato. Per questo esempio è possibile aggiungere un'azione FTP che ottiene il contenuto nuovo o aggiornato.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Azione Aggiungi FTP

L'azione **Ottieni metadati file** ottiene le proprietà di un file che si trovare nel server FTP e l'azione **Ottieni contenuto file** ottiene il contenuto del file in base alle informazioni su tale file sul server FTP. Ad esempio, è possibile aggiungere il trigger dell'esempio precedente e queste azioni per ottenere il contenuto del file dopo l'aggiunta o la modifica del file.

1. Nel trigger o in qualsiasi altra azione selezionare **Nuovo passaggio**.

1. Nella casella di ricerca immettere `ftp` come filtro. Nell'elenco delle azioni selezionare questa azione: **Ottenere i metadati** dei file

   ![Selezionare l'azione "Ottieni metadati file"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Se si ha già una connessione al server FTP e all'account, andare al passaggio successivo. In caso contrario, fornire i dettagli necessari per la connessione e quindi selezionare **Crea**.

   ![Creare la connessione al server FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Quando viene visualizzata l'azione **Ottieni metadati file,** fare clic all'interno della casella **File** in modo che venga visualizzato l'elenco del contenuto dinamico. A questo punto è possibile selezionare le proprietà per gli output dei passaggi precedenti. Nell'elenco del contenuto dinamico, in **Ottieni metadati file**, selezionare la proprietà **Id file,** che fa riferimento alla raccolta in cui il file è stato aggiunto o aggiornato.

   ![Trovare e selezionare la proprietà "Elenco di file ID"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   La proprietà **Id file** viene ora visualizzata nella casella **File.**

   ![Selezionare la proprietà "Elenco di file ID" selezionata](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Ora aggiungi questa azione FTP: **Ottieni il contenuto del file**

   ![Trovare e selezionare l'azione "Ottieni contenuto del file"](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Quando viene visualizzata l'azione **Ottieni contenuto file,** fare clic all'interno della casella **File** in modo che venga visualizzato l'elenco del contenuto dinamico. A questo punto è possibile selezionare le proprietà per gli output dei passaggi precedenti. Nell'elenco del contenuto dinamico, in **Ottieni metadati file**, selezionare la proprietà **Id,** che fa riferimento al file aggiunto o aggiornato.

   ![Trovare e selezionare la proprietà "Id"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   La proprietà **Id** viene ora visualizzata nella casella **File.**

   ![Proprietà "Id" selezionata](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Salvare l'app per la logica.

## <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare che il flusso di lavoro restituisca il contenuto previsto, aggiungere un'altra azione che invii il contenuto dal file caricato o aggiornato.

1. Nell'azione **Ottieni contenuto file** aggiungere un'azione che può inviare il contenuto del file. In questo esempio viene aggiunta l'azione **Invia un messaggio di posta elettronica** per Office 365 Outlook.

   ![Aggiungere un'azione per l'invio di posta elettronica](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Dopo aver visualizzato l'azione, fornire le informazioni e includere le proprietà che si desidera testare. Ad esempio, includere la proprietà **Contenuto file,** che viene visualizzata nell'elenco del contenuto dinamico dopo aver selezionato **Altro** nella sezione Ottieni **contenuto file.**

   ![Specificare informazioni sull'azione di invio del messaggio di posta elettronica](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Salvare l'app per la logica. Per eseguire e attivare l'app per la logica, sulla barra degli strumenti selezionare **Esegui**e quindi aggiungere un file alla cartella FTP monitorata dall'app per la logica.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori dettagli tecnici su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file Swagger del connettore, vedere la [pagina di riferimento del connettore.](https://docs.microsoft.com/connectors/ftpconnector/)

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
