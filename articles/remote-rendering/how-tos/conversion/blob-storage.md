---
title: Usare Archiviazione BLOB di Azure per la conversione dei modelliUse Azure Blob Storage for model conversion
description: Vengono descritti i passaggi comuni per configurare e usare l'archiviazione BLOB per la conversione del modello.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681649"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Usare Archiviazione BLOB di Azure per la conversione dei modelliUse Azure Blob Storage for model conversion

Il servizio di conversione dei modelli richiede l'accesso all'archiviazione BLOB di Azure in modo che possa recuperare i dati di input e [archiviare](model-conversion.md) i dati di output. In questo articolo viene descritto come eseguire i passaggi più comuni.

## <a name="prepare-azure-storage-accounts"></a>Preparare gli account di archiviazione di AzurePrepare Azure Storage accounts

- Creare un account di archiviazione (StorageV2)Create a storage account (StorageV2)
- Creare un contenitore BLOB di input nell'account di archiviazione, ad esempio denominato "arrinput")Create an input blob container in the storage account (for example named "arrinput")
- Creare un contenitore BLOB di output nell'account di archiviazione (ad esempio denominato "arroutput")Create an output blob container in the storage account (for example named "arroutput")

> [!TIP]
> Per istruzioni dettagliate su come configurare l'account di archiviazione, vedere [Guida introduttiva: Convertire un modello per](../../quickstarts/convert-model.md) il rendering

La creazione dell'account di archiviazione e dei contenitori BLOB può essere eseguita con uno degli strumenti seguenti:The creation of the storage account and the blob containers can be done with one of the following tools:

- [Portale di Azure](https://portal.azure.com)
- [az riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Esplora archivi Azure](https://azure.microsoft.com/features/storage-explorer/)
- SDK (C, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Verificare che il rendering remoto di Azure possa accedere all'account di archiviazioneEnsure Azure Remote Rendering can access your storage account

Il Rending remoto di Azure deve recuperare i dati del modello dall'account di archiviazione e riscrivervi i dati.

È possibile concedere l'accesso per il rendering remoto di Azure all'account di archiviazione nei due modi seguenti:You can grant Azure Remote Rendering access to your storage account in the following two ways:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Connettere l'account di archiviazione di Azure con l'account di rendering remoto di AzureConnect your Azure Storage account with your Azure Remote Rendering Account

Seguire i passaggi indicati nella sezione [Creare un account.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Recuperare la gestione della codice di gestione per i contenitori di archiviazioneRetrieve SAS for the storage containers

Le firme di accesso archiviate vengono usate per concedere l'accesso in lettura per l'input e l'accesso in scrittura per l'output. È consigliabile generare nuovi URI ogni volta che un modello viene convertito. Poiché gli URI scadono dopo un certo periodo di tempo, la loro persistenza per un periodo di tempo più lungo può rischiare di interrompere l'applicazione in modo imprevisto.

I dettagli sulla sAS sono disponibili nella [documentazione della sAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

Un URI di sola accesso condiviso può essere generato usando uno dei due:

- modulo di PowerShell az
  - vedere gli [script di PowerShell di esempioSee](../../samples/powershell-example-scripts.md) the example PowerShell scripts
- [az riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Esplora archivi Azure](https://azure.microsoft.com/features/storage-explorer/)
  - clic destro sul contenitore "Ottieni firma di accesso condiviso" (lettura, accesso all'elenco per il contenitore di input, accesso in scrittura per il contenitore di output)
- SDK (C, Python ... )

Un esempio di utilizzo delle firme di accesso condiviso nella conversione degli asset è illustrato in Conversion.ps1 degli script di esempio di [Powershell.](../../samples/powershell-example-scripts.md#script-conversionps1)

## <a name="upload-an-input-model"></a>Caricare un modello di inputUpload an input model

Per iniziare a convertire un modello, è necessario caricarlo, utilizzando una delle seguenti opzioni:

- [Azure Storage Explorer:](https://azure.microsoft.com/features/storage-explorer/) un'interfaccia utente comoda per caricare/scaricare/gestire file nell'archiviazione BLOB di AzureAzure Storage Explorer - a convenient UI to upload/download/manage files on azure blob storage
- [Riga di comando di AzureAzure command line](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Modulo di Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - vedere gli [script di PowerShell di esempioSee the Example PowerShell scripts](../../samples/powershell-example-scripts.md)
- [Utilizzo di un SDK di archiviazione (Python, C ' ... )](https://docs.microsoft.com/azure/storage/)
- [Uso delle API REST di Archiviazione di AzureUsing the Azure Storage REST APIs](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Per un esempio di come caricare i dati per la conversione, fare riferimento a Conversion.ps1 degli script di esempio di [Powershell.](../../samples/powershell-example-scripts.md#script-conversionps1)

## <a name="get-a-sas-uri-for-the-converted-model"></a>Ottenere un URI di accesso condiviso per il modello convertitoGet a SAS URI for the converted model

Questo passaggio è simile al recupero della stringa di [gestione delle chiavi per i contenitori di archiviazione.](#retrieve-sas-for-the-storage-containers) Tuttavia, questa volta è necessario recuperare un URI di accesso condiviso per il file di modello, che è stato scritto nel contenitore di output.

Ad esempio, per recuperare un URI di firma di accesso condiviso tramite [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)fare clic con il pulsante destro del mouse sul file del modello e selezionare "Ottieni firma di accesso condiviso".

Se l'account di archiviazione non è stato connesso all'account di rendering remoto di Azure, è necessaria una firma di accesso condiviso (SAS, Shared Access Signature) per caricare i modelli. Per informazioni su come connettere l'account, vedere [Creare un account.](../create-an-account.md#link-storage-accounts)

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione della conversione del modello](configure-model-conversion.md)
- [L'API REST di conversione del modelloThe model conversion REST API](conversion-rest-api.md)
