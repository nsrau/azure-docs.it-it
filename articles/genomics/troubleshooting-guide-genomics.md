---
title: 'Genomica di Microsoft: guida alla risoluzione dei problemi | Microsoft Docs'
titleSuffix: Azure
description: Altre informazioni sulle strategie di risoluzione dei problemi
keywords: risoluzione dei problemi, errore, debug
services: microsoft-genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: article
ms.date: 10/29/2018
ms.openlocfilehash: 78084e6beac7b390b1ea1afe888030c5224856b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790505"
---
# <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi

Questo articolo presenta alcuni suggerimenti per la risoluzione di alcuni dei problemi comuni che possono verificarsi quando si usa il servizio Genomica di Microsoft, MSGEN.

 Per domande frequenti non correlate alla risoluzione dei problemi, vedere [Domande frequenti](frequently-asked-questions-genomics.md).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Passaggio 1: Individuare i codici di errore associati al flusso di lavoro

Per individuare i messaggi di errore associati al flusso di lavoro, è possibile:

1. Usare la riga di comando e digitare `msgen status`
2. Esaminare il contenuto di standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. Eseguire `msgen status` dalla riga di comando

```bash
msgen status -u URL -k KEY -w ID 
```




Esistono tre argomenti obbligatori:

* URL - URI di base per l'API
* KEY - Chiave di accesso per l'account di Genomica
    * Per trovare i valori per URL e KEY, passare al portale di Azure e aprire la pagina dell'account Genomica di Microsoft. Nell'intestazione **Gestione**, scegliere **Chiavi di accesso**. Qui sono disponibili sia l'URL dell'API e le chiavi di accesso.

  
* ID - ID del flusso di lavoro
    * Per trovare il tipo di ID del flusso di lavoro, digitare il comando `msgen list`. Supponendo che il file di configurazione contenga l'URL e le chiavi di accesso e si trovi nello stesso percorso del file eseguibile di msgen, il comando avrà un aspetto simile al seguente: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        L'output di questo comando avrà un aspetto simile al seguente:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  In alternativa, è possibile includere il percorso del file di configurazione invece di immettere direttamente URL e KEY. Se si includono questi argomenti nella riga di comando e nel file di configurazione, gli argomenti della riga di comando avranno priorità.  

Per un flusso di lavoro con ID 1001 e un file config.txt presente nello stesso percorso del file eseguibile di msgen, il comando avrà un aspetto simile al seguente:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Esaminare il contenuto di standardoutput.txt 
Individuare il contenitore di output per il flusso di lavoro in questione. MSGEN crea una cartella `[workflowfilename].logs.zip` dopo ogni esecuzione del flusso di lavoro. Decomprimere la cartella per visualizzarne il contenuto:

* outputFileList.txt - Elenco dei file di output generati durante il flusso di lavoro
* standarderror.txt - Questo file è vuoto
* standardoutput.txt - Registra tutti i messaggi di stato di primo livello, compresi gli errori, che si sono verificati durante l'esecuzione del flusso di lavoro.
* File di log GATK - Tutti gli altri file nella cartella `logs`

Per risolvere il problema, esaminare il contenuto di standardoutput.txt e prendere nota di eventuali messaggi di errore visualizzati.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Passaggio 2: Provare le procedure consigliate per gli errori comuni

Questa sezione descrive brevemente gli errori comuni restituiti dal servizio Genomica di Microsoft (msgen) e le strategie che è possibile adottare per risolverli. 

Il servizio Genomica di Microsoft (msgen) può generare i due tipi di errori seguenti:

1. Errori interni del servizio: Errori che sono interni al servizio, che non può essere risolti tramite la correzione di parametri o i file di input. In alcuni casi, per risolvere questi errori può essere sufficiente inviare nuovamente il flusso di lavoro.
2. Errori di input: Errori che possono essere risolti usando gli argomenti corretti o correzione dei formati di file.

### <a name="1-internal-service-errors"></a>1. Errori interni del servizio

Un errore interno del servizio non può essere corretto dall'utente. È possibile provare a inviare nuovamente il flusso di lavoro, ma se il problema persiste, contattare il supporto tecnico di Genomica di Microsoft.

| Messaggio di errore                                                                                                                            | Passaggi consigliati per la risoluzione dei problemi                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| An internal error occurred. Try resubmitting the workflow. If you see this error again, contact Microsoft Genomics support for assistance. (Si è verificato un errore interno. Provare a inviare nuovamente il flusso di lavoro. Se l'errore persiste, contattare il supporto tecnico di Genomica di Microsoft per ottenere assistenza.) | Inviare nuovamente il flusso di lavoro. Se il problema persiste, contattare il supporto tecnico di Genomica di Microsoft per ottenere assistenza creando un [ticket](file-support-ticket-genomics.md ) di supporto. |

### <a name="2-input-errors"></a>2. Errori di input

Questi errori possono essere corretti dall'utente. A seconda del tipo di file e del codice di errore, il servizio Genomica di Microsoft restituisce codici di errore distinti. Seguire le procedure consigliate per la risoluzione dei problemi elencate di seguito.

| Tipo di file | Codice di errore | Messaggio di errore                                                                           | Passaggi consigliati per la risoluzione dei problemi                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Qualsiasi          | 701        | Read [readId] has [numberOfBases] bases, but the limit is [maxReadLength] (La lettura [idLettura] ha [numeroBasi] basi, ma il limite è [lunghezzaMaxLettura])           | Il motivo più comune di questo errore è dato dal danneggiamento dei file che ha determinato la concatenazione di due operazioni di lettura. Controllare i file di input. |
| BAM          | 200        |   Unable to read file '[yourFileName]'. (Non è possibile leggere il file '[nomeFile]'.)                                                                                       | Controllare il formato del file BAM. Inviare nuovamente il flusso di lavoro con un file nel formato corretto.                                                                           |
| BAM          | 201        |  Unable to read BAM file [File_name]. (Non è possibile leggere il file BAM [nome_File].)                                                                                      |Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                            |
| BAM          | 202        | Unable to read BAM file [File_name]. (Non è possibile leggere il file BAM [nome_File].) File too small and missing header. (Il file ha dimensioni troppo piccole o l'intestazione è mancante.)                                                                                        | Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                            |
| BAM          | 203        |   Unable to read BAM file [File_name]. (Non è possibile leggere il file BAM [nome_File].) Header of file was corrupt. (L'intestazione del file è danneggiata.)                                                                                      |Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                           |
| BAM          | 204        |    Unable to read BAM file [File_name]. (Non è possibile leggere il file BAM [nome_File].) Header of file was corrupt. (L'intestazione del file è danneggiata.)                                                                                     | Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                           |
| BAM          | 205        |    Unable to read BAM file [File_name]. (Non è possibile leggere il file BAM [nome_File].) Header of file was corrupt. (L'intestazione del file è danneggiata.)                                                                                     | Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                            |
| BAM          | 206        |   Unable to read BAM file [File_name]. (Non è possibile leggere il file BAM [nome_File].) Header of file was corrupt. (L'intestazione del file è danneggiata.)                                                                                      | Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                            |
| BAM          | 207        |  Unable to read BAM file [File_name]. (Non è possibile leggere il file BAM [nome_File].) File truncated near offset [offset]. (Il file è troncato in prossimità dell'offset [offset].)                                                                                       | Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                            |
| BAM          | 208        |   Invalid BAM file. The ReadID [Read_Id] has no sequence in file [File_name]. (Il file BAM non è valido. L'ID lettura [Id_Lettura] non ha dati in sequenza nel file [Nome_file]).                                                                                      | Controllare il formato del file BAM.  Inviare il flusso di lavoro con un file nel formato corretto.                                                                             |
| FASTQ        | 300        |  Unable to read FASTQ file. [File_name] doesn't end with a newline. (Non è possibile leggere il file FASTQ. [Nome_file] non termina con un carattere di nuova riga.)                                                                                     | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                           |
| FASTQ        | 301        |   Unable to read FASTQ file [File_name]. FASTQ record is larger than buffer size at offset: [_offset] (Non è possibile leggere il file FASTQ [Nome_file]. La dimensione del record FASTQ è più grande di quella del buffer in corrispondenza dell'offset: [_offset])                                                                                      | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                         |
| FASTQ        | 302        |     FASTQ Syntax error. File [File_name] has a blank line. (Errore di sintassi FASTQ. Il file [Nome_file] contiene una riga vuota.)                                                                                    | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                         |
| FASTQ        | 303        |       FASTQ Syntax error. File[File_name] has an invalid starting character at offset: [_offset],  line type: [line_type], character: [_char] (Errore di sintassi FASTQ. Il file [Nome_file] contiene un carattere iniziale non valido in corrispondenza dell'offset [_offset], tipo di riga: [line_type], carattere: [_char])                                                                                  | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                         |
| FASTQ        | 304      |  FASTQ Syntax error at readID [_ReadID].  First read of batch doesn't have readID ending in /1 in file [File_name] (Errore di sintassi FASTQ in corrispondenza dell'ID lettura [_IDLettura]. La prima lettura del batch non ha un ID che termina con /1 nel file [nome_File])                                                                                       | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                         |
| FASTQ        | 305        |  FASTQ Syntax error at readID [_readID]. Second read of batch doesn't have readID ending in /2 in file [File_name] (Errore di sintassi FASTQ in corrispondenza dell'ID lettura [_IDLettura]. La seconda lettura del batch non ha un ID che termina con /2 nel file [nome_File])                                                                                      | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                          |
| FASTQ        | 306        |  FASTQ Syntax error at readID [_ReadID]. First read of pair doesn't have an ID that ends in /1 in file [File_name] (Errore di sintassi FASTQ in corrispondenza dell'ID lettura [_IDLettura]. La prima lettura della coppia non ha un ID che termina con /1 nel file [nome_File])                                                                                       | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                          |
| FASTQ        | 307        |   FASTQ Syntax error at readID [_ReadID]. ReadID doesn't end with /1 or/2. File [File_name] can't be used as a paired FASTQ file. (Errore di sintassi FASTQ in corrispondenza dell'ID lettura [_IDLettura]. L'ID lettura non termina con /1 o /2. Il file [Nome_file] non può essere usato come file FASTQ abbinato.)                                                                                      |Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                          |
| FASTQ        | 308        |  FASTQ read error. Reads of both ends responded differently. Did you choose the correct FASTQ files? (Errore di lettura FASTQ. Le letture di entrambe le estremità hanno risposto in modo diverso. Sono stati scelti i file FASTQ corretti?)                                                                                       | Correggere il formato del file FASTQ e inviare nuovamente il flusso di lavoro.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Passaggio 3: Contattare il supporto di genomica di Microsoft

Se i problemi dei processi persistono o se si hanno altre domande, contattare il supporto di Genomica di Microsoft dal portale di Azure. Per altre informazioni su come inviare una richiesta di supporto, vedere [qui](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato descritto come risolvere i problemi comuni con il servizio Genomica di Microsoft. Per altre informazioni e domande frequenti più generali, vedere [Domande comuni](frequently-asked-questions-genomics.md). 
