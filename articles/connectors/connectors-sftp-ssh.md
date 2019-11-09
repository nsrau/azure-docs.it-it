---
title: Connettersi al server SFTP con SSH-app per la logica di Azure
description: Automatizzare le attività che monitorano, creano, gestiscono, inviano e ricevono file per un server SFTP usando SSH e App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: f52fc91d218e1a5448f6e6e7465f6416a04fd67d
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837141"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorare, creare e gestire i file SFTP usando SSH e App per la logica di Azure

Per automatizzare le attività che monitorano, creano, inviano e ricevono file in un server [SFTP (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) usando il protocollo [SSH (Secure Shell)](https://www.ssh.com/ssh/protocol/), è possibile creare e automatizzare i flussi di lavoro di integrazione usando App per la logica di Azure e il connettore SFTP-SSH. SFTP è un protocollo di rete che fornisce l'accesso ai file, il trasferimento di file e la gestione di file su qualsiasi flusso di dati affidabile. Ecco alcuni esempi di attività che è possibile automatizzare:

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, rinominare, aggiornare, creare elenchi ed eliminare file.
* Creare cartelle.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

È possibile usare trigger che monitorano eventi sul server SFTP e rendere l'output disponibile per altre azioni. È possibile usare azioni che eseguono varie attività sul server SFTP. Si può anche fare in modo che altre azioni dell'app per la logica usino l'output delle azioni SFTP. Se ad esempio si recuperano regolarmente file dal server SFTP, è possibile inviare avvisi su tali file e sul relativo contenuto tramite posta elettronica usando il connettore Outlook di Office 365 o Outlook.com. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

Per le differenze tra il connettore SFTP-SSH e il connettore SFTP, vedere la sezione [confrontare SFTP-SSH rispetto a SFTP](#comparison) più avanti in questo argomento.

## <a name="limits"></a>Limiti

* Per impostazione predefinita, le azioni SFTP-SSH possono leggere o scrivere file di *1 GB o di dimensioni inferiori* , ma solo in blocchi di *15 MB* alla volta. Per gestire file di dimensioni superiori a 15 MB, le azioni SFTP-SSH supportano la [suddivisione in blocchi dei messaggi](../logic-apps/logic-apps-handle-large-messages.md), ad eccezione dell'azione copia file, che può gestire solo 15 MB di file. L'azione **Ottieni contenuto file** USA in modo implicito la suddivisione in blocchi dei messaggi.

* I trigger SFTP-SSH non supportano la suddivisione in blocchi. Quando si richiede il contenuto del file, i trigger selezionano solo i file di 15 MB o inferiori. Per ottenere file di dimensioni superiori a 15 MB, seguire invece questo modello:

  * Usare un trigger SFTP-SSH che restituisce le proprietà del file, ad esempio **quando un file viene aggiunto o modificato (solo proprietà)** .

  * Seguire il trigger con l'azione SFTP-SSH **get file content** , che legge il file completo e USA in modo implicito la suddivisione in blocchi dei messaggi.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH e SFTP a confronto

Questa sezione illustra altre differenze importanti tra il connettore SFTP-SSH e il connettore SFTP. SFTP-SSH offre queste funzionalità:

* Usa la [libreria SSH.NET](https://github.com/sshnet/SSH.NET), una libreria SSH (open source Secure Shell) che supporta .NET.

* Per impostazione predefinita, le azioni SFTP-SSH possono leggere o scrivere file di *1 GB o di dimensioni inferiori* , ma solo in blocchi di *15 MB* alla volta.

  Per gestire file di dimensioni superiori a 15 MB, le azioni SFTP-SSH possono usare la [suddivisione in blocchi dei messaggi](../logic-apps/logic-apps-handle-large-messages.md). Tuttavia, l'azione copia file supporta solo file da 15 MB perché tale azione non supporta la suddivisione in blocchi dei messaggi. I trigger SFTP-SSH non supportano la suddivisione in blocchi. Per caricare file di grandi dimensioni, è necessario disporre delle autorizzazioni di lettura e scrittura per la cartella radice sul server SFTP.

* Fornisce l'azione **Crea cartella** che crea una cartella nel percorso specificato nel server SFTP.

* Fornisce l'azione **Rinomina file** che rinomina un file nel server SFTP.

* Memorizza nella cache la connessione al server SFTP *per un massimo di un'ora*, migliorando così le prestazioni e riducendo il numero di tentativi di connessione al server. Per impostare la durata di questo comportamento di memorizzazione nella cache, modificare la proprietà [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) nella configurazione di SSH sul server SFTP.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'indirizzo del server SFTP e le credenziali dell'account che consentono all'app per la logica di accedere all'account SFTP. È anche necessario accedere a una chiave privata SSH e alla password della chiave privata SSH. Per utilizzare la suddivisione in blocchi durante il caricamento di file di grandi dimensioni, è necessario disporre delle autorizzazioni di lettura e scrittura per la cartella radice sul server SFTP. In caso contrario, viene ricevuto un errore "401 non autorizzato".

  > [!IMPORTANT]
  >
  > Il connettore SFTP-SSH supporta *solo* questi formati di chiave privata, algoritmi e impronte digitali:
  >
  > * **Formati di chiave privata**: chiavi RSA (Rivet Shamir Adleman) e DSA (Digital Signature Algorithm) nei formati OpenSSH e SSH.com. Se la chiave privata è in formato di file PuTTy (con estensione PPK), [convertire prima la chiave nel formato di file OpenSSH (con estensione PEM)](#convert-to-openssh).
  >
  > * **Algoritmi di crittografia**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  >
  > * **Impronta digitale**: MD5
  >
  > Dopo aver aggiunto il trigger SFTP-SSH o l'azione desiderata per l'app per la logica, è necessario fornire le informazioni di connessione per il server SFTP. Quando si specifica la chiave privata SSH per questa connessione, ***non immettere o modificare manualmente la chiave***, il che potrebbe causare l'esito negativo della connessione. Al contrario, assicurarsi di ***copiare la chiave*** dal file di chiave privata SSH e ***incollare*** la chiave nei dettagli della connessione. 
  > Per altre informazioni, vedere la sezione [connettersi a SFTP con SSH](#connect) più avanti in questo articolo.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account SFPT. Per iniziare con un trigger SFTP-SSH, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione SFTP-SSH, avviare l'app per la logica con un altro trigger, ad esempio il trigger **Ricorrenza**.

## <a name="how-sftp-ssh-triggers-work"></a>Funzionamento del trigger SFTP-SSH

I trigger SFTP-SSH funzionano eseguendo il polling del file system SFTP e cercando eventuali file modificati dopo l'ultimo polling. Alcuni strumenti consentono di mantenere il timestamp quando il file viene modificato. In questi casi è necessario disabilitare questa funzionalità per consentire il funzionamento del trigger. Ecco alcune delle impostazioni comuni:

| Client SFTP | Azione |
|-------------|--------|
| Winscp | Passare a **Opzioni** > **Preferenze** > **Trasferimento** > **Modifica** > **Mantieni data/ora** > **Disabilitare l'opzione** |
| FileZilla | Passare a **Trasferisci** > **Preserve timestamps of transferred files** (Mantieni timestamp dei file trasferiti)  > **Disattiva** |
|||

Quando un trigger rileva un nuovo file, controlla che sia completo e non parzialmente scritto. Ad esempio, un file potrebbe avere delle modifiche in corso nel momento in cui il trigger controlla il file server. Per evitare la restituzione di un file scritto parzialmente, il trigger prende nota del timestamp del file che contiene le modifiche recenti ma non restituisce immediatamente il file. Il trigger restituisce il file solo durante il nuovo polling del server. In alcuni casi, questo comportamento potrebbe causare un ritardo fino a un massimo del doppio dell'intervallo di polling del trigger.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Convertire la chiave basata su PuTTy in OpenSSH

Se la chiave privata è in formato PuTTy, che usa l'estensione del nome di file. PPK (PuTTy Private Key), convertire prima la chiave nel formato OpenSSH, che usa l'estensione di file PEM (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Sistema operativo basato su UNIX

1. Se gli strumenti PuTTy non sono già installati nel sistema, procedere ora, ad esempio:

   `sudo apt-get install -y putty`

1. Eseguire questo comando, che crea un file che è possibile usare con il connettore SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Ad esempio:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Sistema operativo Windows

1. Se non è già stato fatto, [scaricare lo strumento più recente del generatore PuTTY (puttygen. exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), quindi avviare lo strumento.

1. In questa schermata selezionare **carica**.

   ![Selezionare "carica"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Passare al file di chiave privata in formato PuTTy e selezionare **Apri**.

1. Nel menu **conversioni** selezionare **Esporta chiave OpenSSH**.

   ![Selezionare "Esporta chiave OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Salvare il file di chiave privata con l'estensione del nome di file `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Connettersi a SFTP con SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica vuote, nella casella di ricerca immettere "sftp ssh" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato.

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. Nella casella di ricerca immettere "sftp ssh" come filtro. Nell'elenco delle azioni selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Scegliere il segno più ( **+** ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Specificare le informazioni necessarie per la connessione.

   > [!IMPORTANT]
   >
   > Quando si immette la chiave privata SSH nella proprietà **Chiave privata SSH**, seguire questi passaggi aggiuntivi che consentono di assicurarsi di specificare il valore completo e corretto per questa proprietà. Una chiave non valida provoca un errore di connessione.

   Anche se è possibile usare qualsiasi editor di testo, ecco i passaggi esemplificativi che mostrano come copiare e incollare la chiave usando Notepad.exe.

   1. Aprire il file della chiave privata SSH in un editor di testo. In questi passaggi viene usato il Blocco note come esempio.

   1. Scegliere **Seleziona tutto**dal menu **modifica** del blocco note.

   1. Selezionare **Modifica** > **Copia**.

   1. Nell'azione o nel trigger SFTP-SSH aggiunto, incollare la chiave *completa* copiata nella proprietà **Chiave privata SSH** che supporta più righe.  ***Assicurarsi di incollare*** la chiave. ***Non immettere o modificare la chiave manualmente***.

1. Dopo avere immesso le informazioni per la connessione, scegliere **Crea**.

1. Specificare ora i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

## <a name="examples"></a>Esempi

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>Trigger SFTP-SSH: Quando viene aggiunto o modificato un file

Questo trigger avvia il flusso di lavoro di un'app per la logica quando viene aggiunto o modificato un file in un server SFTP. È ad esempio possibile aggiungere una condizione che controlla il contenuto del file e lo recupera in base al fatto che soddisfi una condizione specificata. Si può quindi aggiungere un'azione che recupera il contenuto del file e lo inserisce in una cartella del server SFTP.

**Esempio riguardante un'organizzazione**: si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti. Si può quindi usare un'azione SFTP come **Ottieni contenuto file** per recuperare il contenuto dell'ordine, elaborarlo ulteriormente e archiviarlo nel database degli ordini.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-azione SSH: Ottieni contenuto tramite il percorso

Questa operazione recupera il contenuto da un file in un server SFTP. Ad esempio, è possibile aggiungere il trigger dell'esempio precedente e una condizione che il contenuto del file deve soddisfare. Se la condizione è true, è possibile eseguire l'azione che recupera il contenuto.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/sftpconnector/) del connettore.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
