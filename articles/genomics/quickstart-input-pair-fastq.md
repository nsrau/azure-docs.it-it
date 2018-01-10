---
title: 'Guida introduttiva: Inviare un flusso di lavoro usando input FASTQ | Microsoft Docs'
titleSuffix: Azure
description: Nella guida introduttiva si presuppone che il client msgen sia installato e che siano stati eseguiti i dati di esempio tramite il servizio.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: f093397803f21c023a2c32e42709ecfcd0e3aec7
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-fastq-file-inputs"></a>Inviare un flusso di lavoro usando input di file FASTQ

Questa guida introduttiva illustra come inviare un flusso di lavoro al servizio Genomica di Microsoft se i file di input sono costituiti da una singola coppia di file FASTQ. In questo argomento si presuppone che sia già stato installato ed eseguito il client `msgen` e che si abbia familiarità con l'uso di Archiviazione di Azure. Se è già stato inviato un flusso di lavoro con i dati di esempio forniti, è possibile proseguire con questa guida introduttiva. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Configurazione: caricare i file FASTQ in Archiviazione di Azure
Si supponga che siano disponibili due file, *reads_1.fq.gz* e *reads_2.fq.gz*, e che i file siano stati caricati nell'account di archiviazione *myaccount* in Azure come **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** e **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**. È necessario che siano disponibili l'URL dell'API e la chiave di accesso. Gli output devono essere disponibili in **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


## <a name="submit-your-job-to-the-msgen-client"></a>Inviare il processo al client `msgen` 

Ecco il set minimo di argomenti che sarà necessario fornire al client `msgen`. Le interruzioni di pagina vengono aggiunte per maggiore chiarezza:

Per Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

Per Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Se si preferisce usare un file di configurazione, deve essere analogo al seguente:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Inviare il file `config.txt` con questa chiamata: `msgen submit -f config.txt`

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata caricata una coppia di file FASTQ in Archiviazione di Azure ed è stato inviato un flusso di lavoro al servizio Genomica di Microsoft tramite il client `msgen` Python. Per altre informazioni sull'invio di flussi di lavoro e su altri comandi che possono essere usati con il servizio Genomica di Microsoft, vedere le [Domande frequenti](frequently-asked-questions-genomics.md). 
