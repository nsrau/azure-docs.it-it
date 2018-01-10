---
title: "Guida introduttiva: Inviare un flusso di lavoro usando più input | Microsoft Docs"
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
ms.openlocfilehash: d410516f807b7914e15bed1fb93ee58d3e340d1e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Inviare un flusso di lavoro usando più input dallo stesso esempio

Questa guida introduttiva illustra come inviare un flusso di lavoro al servizio Genomica di Microsoft se il file di input è costituito da più file FASTQ o BAM **provenienti dallo stesso esempio**. Occorre tuttavia ricordare che **non è possibile** combinare file FASTQ e BAM nello stesso invio.

In questo argomento si presuppone che sia già stato installato ed eseguito il client `msgen` e che si abbia familiarità con l'uso di Archiviazione di Azure. Se è già stato inviato un flusso di lavoro con i dati di esempio forniti, è possibile proseguire con questa guida introduttiva. 


## <a name="multiple-bam-files"></a>Più file BAM

### <a name="upload-your-input-files-to-azure-storage"></a>Caricare i file di input in Archiviazione di Azure
Si supponga che siano disponibili più file BAM come input, *reads.bam*, *additional_reads.bam* e *yet_more_reads.bam*, e che i file siano stati caricati nell'account di archiviazione *myaccount* in Azure. È necessario che siano disponibili l'URL dell'API e la chiave di accesso. Gli output devono essere disponibili in **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Inviare il processo al client `msgen` 

È possibile inviare più file BAM passando tutti i rispettivi nomi all'argomento --input-blob-name-1. Si noti che tutti i file devono provenire dallo stesso esempio, ma l'ordine non è importante. Di seguito sono disponibili invii di esempio da una riga di comando in Windows, in Unix e tramite un file di configurazione. Le interruzioni di riga vengono aggiunte per maggiore chiarezza:


Per Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Per Unix

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Se si preferisce usare un file di configurazione, deve essere analogo al seguente:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Inviare il file `config.txt` con questa chiamata: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Più file FASTQ abbinati

### <a name="upload-your-input-files-to-azure-storage"></a>Caricare i file di input in Archiviazione di Azure
Si supponga che siano disponibili più file FASTQ abbinati come input, *reads_1.fq.gz* e *reads_2.fq.gz*, *additional_reads_1.fq.gz* e *additional_reads_2.fq.gz* e *yet_more_reads_1.fq.gz* e *yet_more_reads_2.fq.gz*. I file sono stati caricati in un account di archiviazione *myaccount* in Azure e sono disponibili l'URL dell'API e la chiave di accesso. Gli output devono essere disponibili in **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Inviare il processo al client `msgen` 

I file FASTQ abbinati non devono solo provenire dallo stesso esempio, ma devono anche essere elaborati insieme.  L'ordine dei nomi di file deve essere rispettato quando vengono passati come argomenti a --input-blob-name-1 e --input-blob-name-2. 

Di seguito sono disponibili invii di esempio da una riga di comando in Windows, in Unix e tramite un file di configurazione. Le interruzioni di riga vengono aggiunte per maggiore chiarezza:


Per Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
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
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Inviare il file `config.txt` con questa chiamata: `msgen submit -f config.txt`

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati caricati più file BAM o file FASTQ abbinati in Archiviazione di Azure ed è stato inviato un flusso di lavoro al servizio Genomica di Microsoft tramite il client `msgen` Python. Per altre informazioni sull'invio di flussi di lavoro e su altri comandi che possono essere usati con il servizio Genomica di Microsoft, vedere le [Domande frequenti](frequently-asked-questions-genomics.md). 