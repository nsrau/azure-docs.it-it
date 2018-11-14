---
title: Connettersi all'account SFTO - App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i processi che monitorano, creano, gestiscono, inviano e ricevono file per un server SFTP tramite SSH usando App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/26/2018
ms.openlocfilehash: 3dbe40476757ba93f33d39f71c46bf58302b3570
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979455"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorare, creare e gestire i file SFTP usando App per la logica di Azure

Per automatizzare le attività che monitorano, creano, inviano e ricevono file in un server [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/), è possibile creare e automatizzare i flussi di lavoro di integrazione usando App per la logica di Azure e il connettore SFTP. SFTP è un protocollo di rete che fornisce l'accesso ai file, il trasferimento di file e la gestione di file su qualsiasi flusso di dati affidabile. Ecco alcuni esempi di attività che è possibile automatizzare: 

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, aggiornare, creare elenchi ed eliminare file.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

Rispetto al [connettore SFTP-SSH](../connectors/connectors-sftp-ssh.md), il connettore SFTP può leggere o scrivere file fino a 50 MB di dimensioni se non si usa la [suddivisione in blocchi per la gestione di messaggi di grandi dimensioni](../logic-apps/logic-apps-handle-large-messages.md). Per i file fino a 1 GB usare il [connettore SFTP-SSH](../connectors/connectors-sftp-ssh.md). Per i file di dimensione maggiore di 1 GB, è possibile usare il connettore SFTP-SSH unito alla [suddivisione in blocchi per i messaggi di grandi dimensioni](../logic-apps/logic-apps-handle-large-messages.md). 

È possibile usare trigger che monitorano eventi sul server SFTP e rendere l'output disponibile per altre azioni. È possibile usare azioni che eseguono varie attività sul server SFTP. È anche possibile fare in modo che altre azioni dell'app per la logica usino l'output delle azioni SFTP. Ad esempio, se si recuperano regolarmente i file dal server SFTP, è possibile inviare avvisi riguardanti tali file e il relativo contenuto mediante messaggi di posta elettronica usando il connettore Outlook di Office 365 o Outlook.com.
Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* L'indirizzo del server SFTP e le credenziali dell'account che consentono all'app per la logica di accedere all'account SFTP. Per usare il protocollo [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) è anche necessario accedere a una chiave privata SSH e alla password della chiave privata SSH. 

  > [!NOTE]
  > 
  > Il connettore SFTP supporta questi formati di chiave privata: OpenSSH, ssh.com e PuTTY
  > 
  > Durante la creazione dell'app per la logica, dopo l'aggiunta del trigger SFTP o dell'azione desiderata, è necessario fornire le informazioni di connessione per il server SFTP. 
  > Se si usa una chiave privata SSH, assicurarsi di ***copiare*** la chiave dal file della chiave privata SSH e ***incollare*** tale chiave nelle informazioni di connessione. ***Non immettere o modificare la chiave manualmente***, in quanto ciò potrebbe causare un errore di connessione. 
  > Per altre informazioni, vedere i passaggi successivi in questo articolo.

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

1. Specificare le informazioni necessarie per la connessione.

   > [!IMPORTANT] 
   >
   > Quando si immette la chiave privata SSH nella proprietà **Chiave privata SSH**, seguire questi passaggi aggiuntivi che consentono di assicurarsi di specificare il valore completo e corretto per questa proprietà. 
   > Una chiave non valida provoca un errore di connessione.
   
   Sebbene sia possibile usare qualsiasi editor di testo, ecco i passaggi esemplificativi che mostrano come copiare e incollare la chiave usando Notepad.exe come esempio.
    
   1. Aprire il file di chiave privata SSH in un editor di testo. 
   Questi passaggi usano il Blocco note come esempio.

   1. Nel menu **Modifica** del Blocco note selezionare **Seleziona tutto**.

   1. Selezionare **Modifica** > **Copia**.

   1. Nell'azione o trigger SFTP aggiunto incollare la chiave *completa* copiata nella proprietà **Chiave privata SSH** che supporta più righe. 
   ***Assicurarsi di incollare*** la chiave. ***Non immettere o modificare la chiave manualmente***.

1. Dopo aver immesso i dettagli della connessione, scegliere **Crea**.

1. Specificare i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="trigger-limits"></a>Limiti dei trigger

I trigger SFTP-SSH eseguono il polling del file system SFTP e cercano tutti i file modificati dall'ultimo polling. Alcuni strumenti consentono di mantenere il timestamp quando il file viene modificato. In questi casi è necessario disabilitare questa funzionalità perché il trigger possa funzionare. Ecco alcune delle impostazioni comuni:

| Client SFTP | Azione | 
|-------------|--------| 
| Winscp | Passare a **Opzioni** > **Preferenze** > **Trasferisci** > **Modifica** > **Preserve timestamp** (Mantieni timestamp)  > **Disattiva** |
| FileZilla | Passare a **Trasferisci** > **Preserve timestamps of transferred files** (Mantieni timestamp dei file trasferiti)  > **Disattiva** | 
||| 

Quando un trigger rileva un nuovo file, controlla che sia completo e non parzialmente scritto. Ad esempio, un file potrebbe avere delle modifiche in corso nel momento in cui il trigger controlla il file server. Per evitare la restituzione di un file scritto parzialmente, il trigger prende nota del timestamp del file che contiene le modifiche recenti ma non restituisce immediatamente il file. Il trigger restituisce il file solo durante il nuovo polling del server. In alcuni casi, questo comportamento potrebbe causare un ritardo fino a un massimo del doppio dell'intervallo di polling del trigger. 

## <a name="examples"></a>Esempi

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Trigger di SFTP: Quando viene aggiunto o modificato un file

Questo trigger avvia il flusso di lavoro di un app per la logica quando un file è stato aggiunto o modificato in un server SFTP. Ad esempio, è possibile aggiungere una condizione che controlla il contenuto del file e determina se leggerlo o meno in base al fatto che soddisfi una condizione specificata. Quindi è possibile aggiungere un'azione che legge il contenuto del file e lo inserisce in una cartella del server SFTP. 

**Esempio riguardante un'organizzazione**: si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti. Si può quindi usare un'azione SFTP come **Recuperare i contenuti del file** per recuperare il contenuto dell'ordine, elaborarlo ulteriormente e archiviarlo nel database degli ordini.

### <a name="sftp-action-get-content"></a>Azione SFTP: recuperare il contenuto

Questa operazione recupera il contenuto da un file in un server SFTP. Ad esempio, è possibile aggiungere il trigger dell'esempio precedente e una condizione che il contenuto del file deve soddisfare. Se la condizione è true, è possibile eseguire l'azione che recupera il contenuto. 

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/sftpconnector/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)