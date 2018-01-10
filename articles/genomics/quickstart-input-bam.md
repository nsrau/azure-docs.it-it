---
title: 'Guida introduttiva: Inviare un flusso di lavoro usando un input di file BAM | Microsoft Docs'
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
ms.openlocfilehash: 9887121593cad4931c86b55012f1c22686adf25f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Inviare un flusso di lavoro usando un input di file BAM

Questa guida introduttiva illustra come inviare un flusso di lavoro al servizio Genomica di Microsoft se il file di input è costituito da un singolo file BAM. In questo argomento si presuppone che sia già stato installato ed eseguito il client `msgen` e che si abbia familiarità con l'uso di Archiviazione di Azure. Se è già stato inviato un flusso di lavoro con i dati di esempio forniti, è possibile proseguire con questa guida introduttiva. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configurazione: caricare il file BAM in Archiviazione di Azure
Si supponga che sia disponibile un singolo file BAM, *reads.bam*, e che il file sia stato caricato nell'account di archiviazione *myaccount* in Azure come **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**. È necessario che siano disponibili l'URL dell'API e la chiave di accesso. Gli output devono essere disponibili in **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



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
  --input-blob-name-1 reads.bam ^
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
  --input-blob-name-1 reads.bam \
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
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Inviare il file `config.txt` con questa chiamata: `msgen submit -f config.txt`

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato caricato un file BAM in Archiviazione di Azure ed è stato inviato un flusso di lavoro al servizio Genomica di Microsoft tramite il client `msgen` Python. Per altre informazioni sull'invio di flussi di lavoro e su altri comandi che possono essere usati con il servizio Genomica di Microsoft, vedere le [Domande frequenti](frequently-asked-questions-genomics.md). 
