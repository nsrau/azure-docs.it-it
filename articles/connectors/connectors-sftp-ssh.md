---
title: Connettersi al server SFTP con SSH
description: Automatizzare le attività che monitorano, creano, gestiscono, inviano e ricevono file per un server SFTP usando SSH e App per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/13/2020
tags: connectors
ms.openlocfilehash: d7fafdd5830ec2825771d4d611a5f4bd5d87260a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393626"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorare, creare e gestire i file SFTP usando SSH e App per la logica di Azure

Per automatizzare le attività che monitorano, creano, inviano e ricevono file in un server [SFTP (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) usando il protocollo [SSH (Secure Shell)](https://www.ssh.com/ssh/protocol/), è possibile creare e automatizzare i flussi di lavoro di integrazione usando App per la logica di Azure e il connettore SFTP-SSH. SFTP è un protocollo di rete che fornisce l'accesso ai file, il trasferimento di file e la gestione di file su qualsiasi flusso di dati affidabile. Ecco alcuni esempi di attività che è possibile automatizzare:

* Monitorare quando i file vengono aggiunti o modificati.
* Ottenere, creare, copiare, rinominare, aggiornare, creare elenchi ed eliminare file.
* Creare cartelle.
* Leggere contenuti e metadati dei file.
* Estrarre archivi nella cartella.

È possibile usare trigger che monitorano eventi sul server SFTP e rendere disponibile l'output per altre azioni. È possibile usare azioni che eseguono varie attività sul server SFTP. Si può anche fare in modo che altre azioni dell'app per la logica usino l'output delle azioni SFTP. Se ad esempio si recuperano regolarmente file dal server SFTP, è possibile inviare avvisi su tali file e sul relativo contenuto tramite posta elettronica usando il connettore Outlook di Office 365 o Outlook.com. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

Per le differenze tra il connettore SFTP-SSH e il connettore SFTP, vedere la sezione Confronto tra [SFTP-SSH e SFTP](#comparison) più avanti in questo argomento.

## <a name="limits"></a>Limiti

* Le azioni SFTP-SSH che supportano la [suddivisione](../logic-apps/logic-apps-handle-large-messages.md) in blocchi possono gestire file fino a 1 GB, mentre le azioni SFTP-SSH che non supportano la suddivisione in blocchi possono gestire file fino a 50 MB. Anche se la dimensione predefinita del blocco è 15 MB, questa dimensione può cambiare dinamicamente, a partire da 5 MB e aumentando gradualmente al massimo 50 MB, in base a fattori quali latenza di rete, tempo di risposta del server e così via.

  > [!NOTE]
  > Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  È possibile eseguire l'override di questo comportamento adattivo quando si [specifica una dimensione di blocco costante](#change-chunk-size) da utilizzare. Questa dimensione può variare da 5 MB a 50 MB. Si supponga, ad esempio, di disporre di un file di 45 MB e di una rete che può supportare tale dimensione senza latenza. La suddivisione in blocchi adattiva comporta diverse chiamate, piuttosto che una chiamata. Per ridurre il numero di chiamate, è possibile provare a impostare una dimensione del blocco di 50 MB. In uno scenario diverso, se l'app per la logica è in timeout, ad esempio quando si usano blocchi da 15 MB, è possibile provare a ridurre la dimensione a 5 MB.

  La dimensione del blocco è associata a una connessione, il che significa che è possibile usare la stessa connessione per le azioni che supportano la suddivisione in blocchi e quindi per le azioni che non supportano la suddivisione in blocchi. In questo caso, la dimensione del blocco per le azioni che non supportano gli intervalli di suddivisione in blocchi da 5 MB a 50 MB. Questa tabella mostra quali azioni SFTP-SSH supportano la suddivisione in blocchi:This table shows which SFTP-SSH actions support chunking:

  | Azione | Supporto per Chunking | Eseguire l'override del supporto delle dimensioni dei blocchi |
  |--------|------------------|-----------------------------|
  | **Copia file** | No | Non applicabile |
  | **Crea file** | Sì | Sì |
  | **Crea cartella** | Non applicabile | Non applicabile |
  | **Elimina file** | Non applicabile | Non applicabile |
  | **Estrai archivio nella cartella** | Non applicabile | Non applicabile |
  | **Recupera contenuto di file** | Sì | Sì |
  | **Recupera contenuto di file tramite percorso** | Sì | Sì |
  | **Ottenere i metadati dei fileGet file metadata** | Non applicabile | Non applicabile |
  | **Recupera metadati di file tramite percorso** | Non applicabile | Non applicabile |
  | **Elenca i file nella cartella** | Non applicabile | Non applicabile |
  | **Rinomina file** | Non applicabile | Non applicabile |
  | **Aggiorna file** | No | Non applicabile |
  ||||

* I trigger SFTP-SSH non supportano la suddivisione in blocchi dei messaggi. Quando si richiede il contenuto del file, i trigger selezionano solo i file di dimensioni pari o inferiori a 15 MB. Per ottenere file di dimensioni superiori a 15 MB, seguire invece questo modello:To get files larger than 15 MB, follow this pattern instead:

  1. Utilizzare un trigger SFTP-SSH che restituisce solo le proprietà del file, ad esempio **Quando un file viene aggiunto o modificato (solo proprietà)**.

  1. Seguire il trigger con l'azione **Ottieni contenuto file** SFTP-SSH, che legge il file completo e utilizza in modo implicito la suddivisione in blocchi dei messaggi.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH e SFTP a confronto

Questa sezione illustra altre differenze importanti tra il connettore SFTP-SSH e il connettore SFTP. SFTP-SSH offre queste funzionalità:

* Utilizza la [libreria SSH.NET](https://github.com/sshnet/SSH.NET), che è una libreria SSH (Secure Shell) open source che supporta .NET.

* Fornisce l'azione **Crea cartella** che crea una cartella nel percorso specificato nel server SFTP.

* Fornisce l'azione **Rinomina file** che rinomina un file nel server SFTP.

* Memorizza nella cache la connessione al server SFTP *per un massimo di un'ora*, migliorando così le prestazioni e riducendo il numero di tentativi di connessione al server. Per impostare la durata di questo comportamento di memorizzazione nella cache, modificare la proprietà [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) nella configurazione di SSH sul server SFTP.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'indirizzo del server SFTP e le credenziali dell'account che consentono all'app per la logica di accedere all'account SFTP. È anche necessario accedere a una chiave privata SSH e alla password della chiave privata SSH. Per utilizzare la suddivisione in blocchi durante il caricamento di file di grandi dimensioni, sono necessarie autorizzazioni di lettura e scrittura per la cartella principale sul server SFTP. In caso contrario, viene visualizzato un errore "401 non autorizzato".

  > [!IMPORTANT]
  >
  > Il connettore SFTP-SSH supporta *solo* questi formati di chiave privata, algoritmi e impronte digitali:
  >
  > * **Formati di chiave privata**: chiavi RSA (Rivest Shamir Adleman) e DSA (Digital Signature Algorithm) nei formati OpenSSH e ssh.com. Se la chiave privata è nel formato di file PuTTY (.ppk), convertire prima la chiave nel formato di [file OpenSSH (.pem)](#convert-to-openssh).
  >
  > * **Algoritmi di crittografia**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  >
  > * **Impronta digitale**: MD5
  >
  > Dopo aver aggiunto il trigger o l'azione SFTP-SSH che si desidera all'app per la logica, è necessario fornire le informazioni di connessione per il server SFTP. Quando si fornisce la chiave privata SSH per questa connessione, ***non immettere o modificare manualmente la chiave***, il che potrebbe causare l'esito negativo della connessione. Assicurarsi invece di ***copiare la chiave*** dal file di chiave privata SSH e ***incollarla*** nei dettagli della connessione. 
  > Per ulteriori informazioni, vedere la sezione [Connessione a SFTP con SSH](#connect) più avanti in questo articolo.

* Informazioni di base sulla [creazione di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole accedere all'account SFPT. Per iniziare con un trigger SFTP-SSH, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione SFTP-SSH, avviare l'app per la logica con un altro trigger, ad esempio il trigger **Ricorrenza**.

## <a name="how-sftp-ssh-triggers-work"></a>Funzionamento dei trigger SFTP-SSH

I trigger SFTP-SSH funzionano eseguendo il polling del file system SFTP e cercando qualsiasi file modificato dopo l'ultimo sondaggio. Alcuni strumenti consentono di mantenere il timestamp quando i file vengono modificati. In questi casi è necessario disabilitare questa funzionalità per consentire il funzionamento del trigger. Ecco alcune delle impostazioni comuni:

| Client SFTP | Azione |
|-------------|--------|
| Winscp | Vai a **Opzioni** > **Preferenze** > **Trasferisci** > **Modifica** > **Mantieni timestamp** > **Disabilita** |
| FileZilla | Vai a **Trasferisci** > **Conserva timestamp dei file** > trasferiti**Disattiva** |
|||

Quando un trigger rileva un nuovo file, controlla che sia completo e non parzialmente scritto. Ad esempio, un file potrebbe avere delle modifiche in corso nel momento in cui il trigger controlla il file server. Per evitare la restituzione di un file scritto parzialmente, il trigger prende nota del timestamp del file che contiene le modifiche recenti ma non restituisce immediatamente il file. Il trigger restituisce il file solo durante il nuovo polling del server. In alcuni casi, questo comportamento potrebbe causare un ritardo fino a un massimo del doppio dell'intervallo di polling del trigger.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Convertire la chiave basata su PuTTY in OpenSSH

Se la chiave privata è in formato PuTTY, che utilizza l'estensione ppk (PuTTY Private Key), convertire prima la chiave nel formato OpenSSH, che utilizza l'estensione .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Sistema operativo basato su Unix

1. Se gli strumenti PuTTY non sono già installati nel sistema, farlo ora, ad esempio:

   `sudo apt-get install -y putty`

1. Eseguire questo comando, che crea un file che è possibile utilizzare con il connettore SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Ad esempio:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Sistema operativo Windows

1. Se non l'hai già fatto, [scarica l'ultimo strumento PuTTY Generator (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)e quindi avvia lo strumento.

1. In questa schermata selezionare **Carica**.

   ![Selezionare "Carica"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Individuare il file della chiave privata nel formato PuTTY e selezionare **Apri**.

1. Dal menu **Conversioni,** selezionare **Esporta tasto OpenSSH**.

   ![Selezionare "Esporta tasto OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Salvare il file della `.pem` chiave privata con l'estensione del nome file.

## <a name="considerations"></a>Considerazioni

In questa sezione vengono descritte le considerazioni da esaminare per i trigger e le azioni del connettore.

<a name="create-file"></a>

### <a name="create-file"></a>Crea file

Per creare un file sul server SFTP, è possibile utilizzare l'azione **Crea file** SFTP-SSH. Quando questa azione crea il file, il servizio App per la logica chiama automaticamente anche il server SFTP per ottenere i metadati del file. Tuttavia, se si sposta il file appena creato prima che il servizio `404` App per `'A reference was made to a file or folder which does not exist'`la logica possa effettuare la chiamata per ottenere i metadati, viene visualizzato un messaggio di errore, . Per ignorare la lettura dei metadati del file dopo la creazione del file, attenersi alla procedura per [aggiungere e impostare la proprietà **Get all file metadata** su **No**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Connettersi a SFTP con SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per le app per la logica `sftp ssh` vuote, nella casella di ricerca immettere il filtro. Nell'elenco dei trigger selezionare il trigger desiderato.

   -oppure-

   Per le app per la logica esistenti, nell'ultimo passaggio in cui si vuole aggiungere un'azione selezionare **Nuovo passaggio**. Nella casella di ricerca immettere `sftp ssh` come filtro. Nell'elenco delle azioni selezionare l'azione desiderata.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Specificare le informazioni necessarie per la connessione.

   > [!IMPORTANT]
   >
   > Quando si immette la chiave privata SSH nella proprietà **Chiave privata SSH**, seguire questi passaggi aggiuntivi che consentono di assicurarsi di specificare il valore completo e corretto per questa proprietà. Una chiave non valida provoca un errore di connessione.

   Anche se è possibile usare qualsiasi editor di testo, ecco i passaggi esemplificativi che mostrano come copiare e incollare la chiave usando Notepad.exe.

   1. Aprire il file della chiave privata SSH in un editor di testo. In questi passaggi viene usato il Blocco note come esempio.

   1. Scegliere **Seleziona tutto**dal menu **Modifica** blocco note .

   1. Selezionare **Modifica** > **copia**.

   1. Nell'azione o nel trigger SFTP-SSH aggiunto, incollare la chiave *completa* copiata nella proprietà **Chiave privata SSH** che supporta più righe.  ***Assicurarsi di incollare*** la chiave. ***Non immettere o modificare manualmente il tasto***.

1. Al termine dell'immissione dei dettagli di connessione, selezionare **Crea**.

1. Specificare ora i dettagli necessari per l'azione o il trigger selezionato e continuare a creare il flusso di lavoro dell'app per la logica.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Ignora dimensione blocco

Per eseguire l'override del comportamento adattivo predefinito utilizzato dalla suddivisione in blocchi, è possibile specificare una dimensione di blocco costante da 5 MB a 50 MB.

1. Nell'angolo superiore destro dell'azione selezionare il pulsante con i puntini di sospensione (**...**), quindi scegliere **Impostazioni**.

   ![Aprire le impostazioni SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. In **Trasferimento contenuto**, nella proprietà Dimensione **blocco** immettere un valore intero da `5` a , `50`ad esempio: 

   ![Specificare invece la dimensione del blocco da utilizzareSpecify chunk size to use instead](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Al termine, fare clic su **Fine**.

## <a name="examples"></a>Esempi

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>Trigger SFTP-SSH: Quando viene aggiunto o modificato un file

Questo trigger avvia il flusso di lavoro di un'app per la logica quando viene aggiunto o modificato un file in un server SFTP. È ad esempio possibile aggiungere una condizione che controlla il contenuto del file e lo recupera in base al fatto che soddisfi una condizione specificata. Si può quindi aggiungere un'azione che recupera il contenuto del file e lo inserisce in una cartella del server SFTP.

**Esempio riguardante un'organizzazione**: si potrebbe usare questo trigger per monitorare una cartella SFTP per nuovi file di ordini dei clienti. Si può quindi usare un'azione SFTP come **Ottieni contenuto file** per recuperare il contenuto dell'ordine, elaborarlo ulteriormente e archiviarlo nel database degli ordini.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - Azione SSH: Ottenere il contenuto del file utilizzando il percorso

Questa azione ottiene il contenuto da un file su un server SFTP specificando il percorso del file. Ad esempio, è possibile aggiungere il trigger dell'esempio precedente e una condizione che il contenuto del file deve soddisfare. Se la condizione è true, è possibile eseguire l'azione che recupera il contenuto.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>Risolvere gli errori

In questa sezione vengono descritte le possibili soluzioni a errori o problemi comuni.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>Errore 404: "È stato fatto un riferimento a un file o a una cartella che non esiste"

Questo errore può verificarsi quando l'app per la logica crea un nuovo file nel server SFTP tramite l'azione **Crea file** SFTP-SSH, ma il file appena creato viene quindi spostato immediatamente prima che il servizio App per la logica possa ottenere i metadati del file. Quando l'app per la logica esegue l'azione **Crea file,** il servizio App per la logica chiama automaticamente anche il server SFTP per ottenere i metadati del file. Tuttavia, se il file viene spostato, il servizio App per `404` la logica non riesce più a trovare il file in modo da ottenere il messaggio di errore.

Se non è possibile evitare o ritardare lo spostamento del file, è possibile ignorare la lettura dei metadati del file dopo la creazione del file attenendosi alla seguente procedura:

1. Nell'azione **Crea file** aprire l'elenco **Aggiungi nuovo parametro,** selezionare la proprietà **Ottieni tutti i metadati** del file e impostare il valore su **No**.

1. Se questi metadati del file sono necessari in un secondo momento, è possibile utilizzare l'azione **Ottieni metadati file.**

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori dettagli tecnici su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file Swagger del connettore, vedere la [pagina di riferimento del connettore.](https://docs.microsoft.com/connectors/sftpwithssh/)

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
