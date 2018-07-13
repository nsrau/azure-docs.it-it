---
title: 'Genomica di Microsoft: guida alla risoluzione dei problemi | Microsoft Docs'
titleSuffix: Azure
description: Altre informazioni sulle strategie di risoluzione dei problemi
keywords: risoluzione dei problemi, errore, debug
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: d3991bdbcd9c3dcd08572dc92cc75aaebb02b133
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/04/2018
ms.locfileid: "34627365"
---
# <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi
Questa panoramica descrive le strategie per risolvere i problemi comuni quando si usa il servizio Genomica di Microsoft. Per domande frequenti generali, vedere [Domande comuni](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Come è possibile controllare lo stato del processo?
È possibile controllare lo stato del flusso di lavoro chiamando `msgen status` dalla riga di comando, come illustrato. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Esistono tre argomenti obbligatori:
* URL - URI di base per l'API
* KEY - Chiave di accesso per l'account di Genomica 
* ID - ID del flusso di lavoro

Per trovare i valori per URL e KEY, passare al portale di Azure e aprire la pagina dell'account di Genomica. Nell'intestazione **Gestione**, scegliere **Chiavi di accesso**. Qui sono disponibili sia l'URL dell'API e le chiavi di accesso.

In alternativa, è possibile includere il percorso del file di configurazione invece di immettere direttamente URL e KEY. Si noti che se si includono questi argomenti nella riga di comando e nel file di configurazione, gli argomenti della riga di comando avranno la precedenza. 

Dopo la chiamata di `msgen status`, verrà visualizzato un messaggio descrittivo, che indica se il flusso di lavoro ha avuto esito positivo o un motivo per l'errore del processo. 


## <a name="get-more-information-about-my-workflow-status"></a>Ottenere altre informazioni sullo stato del flusso di lavoro

Per ottenere altre informazioni sui motivi del possibile esito negativo di un processo, è possibile esplorare i file di log generati durante il flusso di lavoro. Nel contenitore di output dovrebbe essere disponibile una cartella `[youroutputfilename].logs.zip`.  Se si decomprime la cartella verranno visualizzati gli elementi seguenti:

* outputFileList.txt - Elenco dei file di output generati durante il flusso di lavoro
* standarderror.txt - Questo file è vuoto
* standardoutput.txt - Contiene la registrazione di primo livello del flusso di lavoro 
* File di log GATK - Tutti gli altri file nella cartella `logs`

Il file `standardoutput.txt` è un buon punto di partenza per determinare i motivi per cui il flusso di lavoro non è riuscito, perché include più informazioni di basso livello del flusso di lavoro. 

## <a name="common-issues-and-how-to-resolve-them"></a>Problemi comuni e come risolverli
Questa sezione descrive brevemente i problemi comuni e come risolverli.

### <a name="fastq-files-are-unmatched"></a>File Fastq non corrispondenti
I file Fastq devono differire solo per la presenza di /1 o /2 alla fine nell'identificatore di esempio. Se vengono inviati accidentalmente file FASTQ non corrispondenti, potrebbero essere visualizzati i messaggi di errore seguenti quando si chiama `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Per risolvere questo problema, controllare se i file fastq inviati al flusso di lavoro sono effettivamente un set corrispondente. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Errore di caricamento del file con estensione bam. Il BLOB di output esiste già e l'opzione di sovrascrittura è stata impostata su False.
Se viene visualizzato il messaggio di errore seguente, `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, la cartella di output contiene già un file di output con lo stesso nome.  Eliminare il file di output esistente o attivare l'opzione di sovrascrittura nel file di configurazione, quindi inviare di nuovo il flusso di lavoro.

### <a name="when-to-contact-microsoft-genomics-support"></a>Quando contattare il supporto di Genomica di Microsoft
Se vengono visualizzati i messaggi di errore seguenti, si è verificato un errore interno. 

* `Error locating input files on worker machine`
* `Process management failure`

Provare a inviare di nuovo il flusso di lavoro. Se i problemi dei processi persistono o se si hanno altre domande, contattare il supporto di Genomica di Microsoft dal portale di Azure. Per altre informazioni su come inviare una richiesta di supporto, vedere [qui](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come risolvere i problemi comuni con il servizio Genomica di Microsoft. Per altre informazioni e domande frequenti più generali, vedere [Domande comuni](frequently-asked-questions-genomics.md). 