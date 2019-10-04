---
title: Inviare un flusso di lavoro usando input FASTQ - Genomics di Microsoft
titleSuffix: Azure
description: Questo articolo si presuppone che si è installato il client msgen e sono stati eseguiti correttamente i dati di esempio tramite il servizio.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 2662a8f52c58a39916e5789fa9ed7fadd91216c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60333673"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>Inviare un flusso di lavoro usando input FASTQ in Genomica di Microsoft

Questo articolo viene illustrato come inviare un flusso di lavoro al servizio Microsoft Genomics se i file di input sono una singola coppia di file FASTQ. In questo argomento si presuppone che sia già stato installato ed eseguito il client `msgen` e che si abbia familiarità con l'uso di Archiviazione di Azure. Se è già stato inviato un flusso di lavoro usando i dati di esempio forniti, si è pronti per procedere con questo articolo. 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>Configurazione: Caricare i file FASTQ in archiviazione di Azure
Si supponga che siano disponibili due file, *reads_1.fq.gz* e *reads_2.fq.gz*, e che i file siano stati caricati nell'account di archiviazione *myaccount* in Azure come **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** e **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>** . È necessario che siano disponibili l'URL dell'API e la chiave di accesso. Gli output devono essere disponibili in **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** .


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
In questo articolo è stata caricata una coppia di file FASTQ in Archiviazione di Azure ed è stato inviato un flusso di lavoro al servizio Genomica di Microsoft tramite il client `msgen` Python. Per altre informazioni sull'invio di flusso di lavoro e altri comandi è possibile usare con il servizio Microsoft Genomics, vedere la [domande frequenti su](frequently-asked-questions-genomics.md). 
