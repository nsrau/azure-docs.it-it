---
title: Connettersi all'account SFTO da App per la logica di Azure | Microsoft Docs
description: Automatizzare le attività e i flussi di lavoro che monitorano, creano, gestiscono, inviano e ricevono file per un server SFTP usando App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: 2250c6952aeac7b10dcb1a1a35419941e5cad507
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233209"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Monitorare, creare e gestire i file SFTP usando App per la logica di Azure e il connettore SFTP-SSH

Con App per la logica di Azure e il connettore SFTP-SSH, è possibile creare attività automatizzate e flussi di lavoro che monitorano, creano, inviano e ricevono file attraverso il proprio account in un server [SFTP](https://www.ssh.com/ssh/sftp/); possono inoltre eseguire altre azioni, quali:

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, rinominare, aggiornare, creare elenchi ed eliminare file.
* Creare una cartella.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

È possibile usare i trigger per ottenere risposte dal server SFTP e rendere l'output disponibile per altre azioni. È possibile usare azioni nelle app per la logica per eseguire attività sul server SFTP. È anche possibile che altre azioni usino l'output delle azioni di SFTP. Ad esempio, se si recuperano regolarmente i file dal server SFTP, è possibile inviare messaggi di posta elettronica riguardanti tali file e il relativo contenuto usando il connettore Outlook di Office 365 o Outlook.com.
Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>SFTP-SSH e SFTP

Di seguito sono elencate alcune differenze principali tra il connettore SFTP-SSH e il connettore [SFTP](../connectors/connectors-create-api-sftp.md). Il connettore SFTP-SSH fornisce queste funzionalità:

* Usa la libreria <a href="https://github.com/sshnet/SSH.NET" target="_blank">**SSH.NET**</a> che è una libreria Secure Shell (SSH) open source per .NET.

* Fornisce il supporto per file di grandi dimensioni fino a **1GB**. Il connettore può leggere o scrivere i file che sono di dimensioni fino a 1 GB.

* Fornisce l'azione **Crea cartella** che crea una cartella nel percorso specificato nel server SFTP.

* Fornisce l'azione **Rinomina file** che rinomina un file nel server SFTP.

* Memorizza nella cache la connessione al server SFTP, che migliora le prestazioni e riduce il numero di tentativi di connessione nel server. 

  È possibile controllare la durata della connessione di memorizzazione nella cache impostando la proprietà <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank">**ClientAliveInterval**</a> nel server SFTP. 

## <a name="how-trigger-polling-works"></a>Come funziona il polling dei trigger

I trigger SFTP-SSH eseguono il polling del sistema di file SFTP e ricercano tutti i file modificati dall'ultimo polling. Alcuni strumenti consentono di mantenere il timestamp delle modifiche ai file: in questi casi è necessario disabilitare questa funzionalità per il funzionamento del trigger. Ecco alcune delle impostazioni comuni:

| Client SFTP | Azione | 
|-------------|--------| 
| Winscp | Options -> Preferences… (Opzioni-> Preferenze) -> Transfer -> Edit… (Trasferisci -> Modifica) -> Preserve timestamp -> Disable (Preserva data e ora -> Disabilita) |
| FileZilla | Transfer -> Preserve timestamps of transferred files -> Disable (Trasferisci ->Preserva data e ora dei file trasferiti -> Disabilita) | 
||| 

Quando un trigger rileva un nuovo file, controlla che sia completo e non parzialmente scritto. Ad esempio, un file potrebbe avere delle modifiche in corso nel momento in cui il trigger controlla il file server. Per evitare la restituzione di un file scritto parzialmente, il trigger prende nota del timestamp del file che contiene le modifiche recenti ma non restituisce immediatamente il file. Il trigger restituisce il file solo durante il nuovo polling del server. In alcuni casi, questo comportamento potrebbe causare un ritardo fino a un massimo del doppio dell'intervallo di polling del trigger. 

Quando richiede il contenuto del file, il trigger non recupera i file di dimensioni superiori a 50 MB. Per acquisire i file di dimensioni superiori a 50 MB, seguire questo modello:

* Usare un trigger che restituisce le proprietà del file, ad esempio **quando un file viene aggiunto o modificato (solo proprietà)**. 
* Fare seguire al trigger un'azione che legga il file completo, ad esempio **Ottieni contenuto di file tramite percorso**.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* L'indirizzo del server host SFTP e le credenziali dell'account che autorizzano l'app per la logica alla creazione di una connessione e all'accesso all'account SFTP.

  Copiare e incollare il contenuto completo della chiave privata SSH nella proprietà **Chiave privata SSH** seguendo il formato su più righe. 
  Ecco i passaggi di esempio che illustrano come fornire la chiave privata SSH usando Notepad.exe:
    
  1. Aprire il file della chiave privata SSH in Notepad.exe
  2. Nel menu **Modifica** selezionare **Seleziona tutto**.
  3. Selezionare **Modifica** > **Copia**.
  4. Quando si crea la connessione incollare la chiave nella proprietà **Chiave privata SSH**. Non modificare manualmente la proprietà **Chiave privata SSH**.

     Il connettore usa la libreria SSH.NET, che supporta questi formati di chiave privati SSH e solo l'algoritmo impronta digitale MD5:

     * RSA 
     * DSA

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
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)