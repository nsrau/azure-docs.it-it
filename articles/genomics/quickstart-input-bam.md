---
title: Inviare un flusso di lavoro utilizzando un file BAM input - Genomics di Microsoft
titleSuffix: Azure
description: Questo articolo si presuppone che si è installato il client msgen e sono stati eseguiti correttamente i dati di esempio tramite il servizio.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 329ac374edbffa65a1b654a05a4934828f25d5de
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435083"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Inviare un flusso di lavoro usando un input di file BAM

Questo articolo illustra come inviare un flusso di lavoro al servizio Microsoft Genomics se il file di input è un singolo file BAM. In questo argomento si presuppone che sia già stato installato ed eseguito il client `msgen` e che si abbia familiarità con l'uso di Archiviazione di Azure. Se è già stato inviato un flusso di lavoro usando i dati di esempio forniti, si è pronti per procedere con questo articolo. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configurazione: Caricare il file BAM in archiviazione di Azure
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
