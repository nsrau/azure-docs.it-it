---
title: Usare l'archiviazione BLOB di Azure per la conversione dei modelli
description: Vengono descritti i passaggi comuni per la configurazione e l'utilizzo dell'archiviazione BLOB per la conversione del modello.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681649"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Usare l'archiviazione BLOB di Azure per la conversione dei modelli

Il servizio di [conversione del modello](model-conversion.md) richiede l'accesso all'archivio BLOB di Azure in modo da poter recuperare i dati di input e archiviare i dati di output. Questo articolo descrive come eseguire le operazioni più comuni.

## <a name="prepare-azure-storage-accounts"></a>Preparare gli account di archiviazione di Azure

- Creazione di un account di archiviazione (archiviazione v2)
- Creare un contenitore BLOB di input nell'account di archiviazione (ad esempio, denominato "arrInput")
- Creare un contenitore BLOB di output nell'account di archiviazione (ad esempio, denominato "arroutput")

> [!TIP]
> Per istruzioni dettagliate su come configurare l'account di archiviazione, vedere [Guida introduttiva: convertire un modello per il rendering](../../quickstarts/convert-model.md)

La creazione dell'account di archiviazione e dei contenitori BLOB può essere eseguita con uno degli strumenti seguenti:

- [Azure portal](https://portal.azure.com)
- [riga di comando AZ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- SDK (C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Verificare che il rendering remoto di Azure possa accedere all'account di archiviazione

Il rendering remoto di Azure deve recuperare i dati del modello dall'account di archiviazione e scrivere di nuovo i dati.

È possibile concedere l'accesso per il rendering remoto di Azure all'account di archiviazione nei due modi seguenti:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Connettere l'account di archiviazione di Azure all'account di rendering remoto di Azure

Seguire i passaggi indicati nella sezione [creare un account](../create-an-account.md#link-storage-accounts) .

### <a name="retrieve-sas-for-the-storage-containers"></a>Recuperare SAS per i contenitori di archiviazione

Le firme di accesso (SAS) archiviate vengono usate per concedere l'accesso in lettura per l'input e per l'accesso in scrittura per l'output. Si consiglia di generare nuovi URI ogni volta che viene convertito un modello. Poiché gli URI scadono dopo un certo periodo di tempo, la loro permanenza per una durata più lunga può compromettere l'applicazione in modo imprevisto.

Per informazioni dettagliate sulle associazioni di firma di accesso condiviso, vedere la [documentazione di SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

È possibile generare un URI di firma di accesso condiviso utilizzando uno dei seguenti:

- AZ PowerShell module
  - vedere gli [script di PowerShell di esempio](../../samples/powershell-example-scripts.md)
- [riga di comando AZ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - fare clic con il pulsante destro del mouse sul contenitore "Ottieni firma di accesso condiviso" (lettura, elencare l'accesso per il contenitore di input, accesso in scrittura per il contenitore
- SDK (C#, Python...)

Un esempio di come usare le firme di accesso condiviso nella conversione degli asset viene visualizzato in conversione. ps1 degli [script di esempio di PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Caricare un modello di input

Per iniziare a convertire un modello, è necessario caricarlo, usando una delle opzioni seguenti:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) : una comoda interfaccia utente per caricare, scaricare e gestire i file nell'archivio BLOB di Azure
- [Riga di comando di Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Modulo di Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - vedere gli [script di PowerShell di esempio](../../samples/powershell-example-scripts.md)
- [Uso di un SDK di archiviazione (Python, C#...)](https://docs.microsoft.com/azure/storage/)
- [Uso delle API REST di archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Per un esempio di come caricare i dati per la conversione, vedere conversione. ps1 degli [script di esempio di PowerShell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Ottenere un URI di firma di accesso condiviso per il modello convertito

Questo passaggio è simile al [recupero di SAS per i contenitori di archiviazione](#retrieve-sas-for-the-storage-containers). Tuttavia, questa volta è necessario recuperare un URI di firma di accesso condiviso per il file di modello, che è stato scritto nel contenitore di output.

Ad esempio, per recuperare un URI di firma di accesso condiviso tramite il [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), fare clic con il pulsante destro del mouse sul file del modello e selezionare "Ottieni firma di accesso condiviso".

Una firma di accesso condiviso (SAS) per caricare i modelli è necessaria se l'account di archiviazione non è stato connesso all'account di rendering remoto di Azure. Per informazioni su come connettere l'account, è possibile [creare un account](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione della conversione del modello](configure-model-conversion.md)
- [API REST per la conversione di modelli](conversion-rest-api.md)
