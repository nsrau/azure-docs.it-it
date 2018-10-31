---
title: Connettersi all'account SFTO da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro che monitorano, creano, gestiscono, inviano e ricevono file per un server SFTP usando App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/11/2018
ms.openlocfilehash: 77a76aa4fbb051e5999053279798c1b0147ae8e9
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166781"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorare, creare e gestire i file SFTP usando App per la logica di Azure

Con App per la logica di Azure e il connettore SFTP, è possibile creare attività automatizzate e flussi di lavoro che monitorano, creano, inviano e ricevono file attraverso il proprio account in un server [SFTP](https://www.ssh.com/ssh/sftp/); possono inoltre eseguire altre azioni, quali:

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, aggiornare, creare elenchi ed eliminare file.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

È possibile usare i trigger per ottenere risposte dal server SFTP e rendere l'output disponibile per altre azioni. È possibile usare azioni nelle app per la logica per eseguire attività sul server SFTP. È anche possibile che altre azioni usino l'output delle azioni di SFTP. Ad esempio, se si recuperano regolarmente i file dal server SFTP, è possibile inviare messaggi di posta elettronica riguardanti tali file e il relativo contenuto usando il connettore Outlook di Office 365 o Outlook.com. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Per i file di dimensioni comprese tra 50 MB e 1 GB, usare il [connettore SFTP-SSH](../connectors/connectors-sftp-ssh.md). Il connettore SFTP supporta solo file con dimensioni massime di 50 MB, a meno che non si usi la [divisione in blocchi per gestire i messaggi di grandi dimensioni](../logic-apps/logic-apps-handle-large-messages.md). 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Credenziali dell'account e indirizzo del server host SFTP

   Le credenziali autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account SFTP.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account SFPT. Per iniziare con un trigger di SFTP, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione di SFTP, avviare l'app per la logica con un altro trigger, ad esempio, il trigger **Ricorrenza**.

## <a name="connect-to-sftp"></a>Connettersi a SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere "sftp" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato. 

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 
   Nella casella di ricerca immettere "sftp" come filtro. 
   Nell'elenco delle azioni selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
   Scegliere il segno più (**+**) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Specificare i dettagli necessari per la connessione, quindi scegliere **Creare**.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="examples"></a>Esempi

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Trigger di SFTP: Quando viene aggiunto o modificato un file

Questo trigger avvia il flusso di lavoro di un app per la logica quando il trigger rileva che un file è stato aggiunto o modificato in un server SFTP. Ad esempio, è possibile aggiungere una condizione che controlla il contenuto del file e decide se leggerlo, a seconda che il contenuto soddisfi una condizione specificata. Infine, è possibile aggiungere un'azione che legga il contenuto del file e inserisca tale contenuto in una cartella nel server SFTP. 

**Esempio riguardante un'organizzazione**: si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti. È quindi possibile usare un'azione di SFTP come **Recuperare i contenuti del file** per recuperare il contenuto dell'ordine per elaborarlo ulteriormente e archiviarlo nel database degli ordini.

### <a name="sftp-action-get-content"></a>Azione SFTP: recuperare il contenuto

Questa operazione recupera il contenuto da un file in un server SFTP. Ad esempio, è possibile aggiungere il trigger dell'esempio precedente e una condizione che il contenuto del file deve soddisfare. Se la condizione è true, è possibile eseguire l'azione che recupera il contenuto. 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/sftpconnector/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)