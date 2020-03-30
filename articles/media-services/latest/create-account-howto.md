---
title: Creare un account di Servizi multimediali di Azure
description: Questa esercitazione illustra i passaggi per la creazione di un account di Servizi multimediali di Azure.This tutorial walks you through steps of creating an Azure Media Services account.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478799"
---
# <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

Per avviare le operazioni di crittografia, codifica, analisi, gestione e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. L'account di Servizi multimediali deve essere associato a uno o più account di archiviazione.

> [!NOTE]
> L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi.

Questo articolo descrive i passaggi per la creazione di un nuovo account di Servizi multimediali di Azure.This article describes steps for creating a new Azure Media Services account. Scegliere una delle seguenti schede.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Il portale di Azure offre un modo per creare rapidamente un account di Servizi multimediali di Azure.The Azure portal provides a way to quickly create an Azure Media Services account. È possibile utilizzare l'account per accedere ai servizi multimediali che consentono di archiviare, crittografare, codificare, gestire e distribuire in streaming contenuti multimediali in Azure.

Attualmente, è possibile usare il portale di Azure per:Currently, you can use the [Azure portal](https://portal.azure.com/) to:

* gestire Media Services v3 [Live Events](live-events-outputs-concept.md), 
* visualizza (non gestisce) [Risorse](assets-concept.md)v3 , 
* [ottenere informazioni sull'accesso alle API](access-api-portal.md). 

Per tutte le altre attività di gestione, ad esempio [Trasformazioni e processi](transforms-jobs-concept.md) e [Protezione contenuto,](content-protection-overview.md)utilizzare l'API [REST](https://aka.ms/ams-v3-rest-ref), l'interfaccia della riga [di comando](https://aka.ms/ams-v3-cli-ref)o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.

Questo articolo illustra come creare un account di Servizi multimediali usando il portale di Azure.

### <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Fare clic su **Crea una risorsa** > **Media** > **Services**.
1. Nella sezione **Creare un account di Servizi multimediali** immettere i valori obbligatori.
    
    | Nome | Descrizione |
    | ---|---|
    |**Nome account**|Immettere il nome del nuovo account di Servizi multimediali. Un nome di account di Servizi multimediali deve essere composto solo da lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.|
    |**Sottoscrizione**|Se si dispone di più sottoscrizioni, selezionarne una dall'elenco delle sottoscrizioni di Azure a cui si ha accesso.|
    |**Gruppo di risorse**|Selezionare la risorsa nuova o esistente. Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Scopri di più [qui](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Percorso**|Selezionare l'area geografica che verrà utilizzata per archiviare i record multimediali e di metadati per l'account di Servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. |
    |**Account di archiviazione**|Selezionare un account di archiviazione per fornire l'archiviazione BLOB del contenuto multimediale dall'account di Servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account di Servizi multimediali oppure è possibile crearne uno nuovo. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.<br/><br/>È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. È possibile usare il portale di Azure per aggiungere account di archiviazione secondari. Per altre informazioni, vedere Account di Archiviazione di Azure con account di Servizi multimediali di Azure.For more information, see [Azure Storage accounts with Azure Media Services accounts.](storage-account-concept.md)<br/><br/>L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi.|
    
1. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
1. Fare clic su **Crea** nella parte inferiore del form.

    Quando viene creato l'account di Servizi multimediali, viene aggiunto un endpoint di streaming **predefinito** all'account nello stato **Arrestato.** Per avviare lo streaming del contenuto e sfruttare la creazione di [pacchetti dinamici](dynamic-packaging-overview.md) e la [crittografia dinamica,](content-protection-overview.md)l'endpoint di streaming da cui si desidera trasmettere il contenuto deve essere nello stato **In esecuzione.** 

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Impostare la sottoscrizione di Azure

Nel comando seguente specificare l'ID della sottoscrizione di Azure che si vuole usare per l'account di Servizi multimediali. È possibile visualizzare un elenco di sottoscrizioni a cui è possibile accedere passando a [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando seguente. Un gruppo di risorse di Azure è un contenitore logico in cui vengono distribuite e gestite risorse di Azure come gli account di Servizi multimediali di Azure e gli account di archiviazione associati.

È possibile `amsResourceGroup` sostituire con il vostro valore.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Quando si crea un account di Servizi multimediali, è necessario specificare il nome di una risorsa dell'account di Archiviazione di Azure. L'account di archiviazione specificato è collegato all'account personale di Servizi multimediali. Per altre informazioni sull'uso degli account di archiviazione in Servizi multimediali, vedere [Account di archiviazione](storage-account-concept.md).

È necessario disporre di un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. Servizi multimediali supporta account **Utilizzo generico v2** (GPv2) o **Utilizzo generico v1** (GPv1). Gli account solo BLOB non sono consentiti come **primari**. Per altre informazioni sugli account di archiviazione, vedere [Opzioni di account di archiviazione di Azure](../../storage/common/storage-account-options.md). 

In questo esempio viene creato un account di archiviazione con ridondanza locale Standard per utilizzo generico v2. Se si vogliono provare gli account di archiviazione, usare `--sku Standard_LRS`. Tuttavia, quando si sceglie SKU per la produzione, è consigliabile usare `--sku Standard_RAGRS` che offre la replica geografica per la continuità aziendale. Per altre informazioni, vedere [account di archiviazione](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
Il comando seguente consente di creare un account di archiviazione che verrà associato all'account di Servizi multimediali. Nello script seguente è possibile sostituire `storageaccountforams` con il valore personale. `amsResourceGroup`deve corrispondere al valore fornito per il gruppo di risorse nel passaggio precedente. Il nome dell'account di archiviazione deve avere una lunghezza inferiore a 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

Il comando seguente dell'interfaccia della riga di comando di Azure consente di creare un nuovo account di Servizi multimediali. È possibile sostituire i `amsaccount` `storageaccountforams` valori seguenti: (deve corrispondere al `amsResourceGroup` valore fornito per l'account di archiviazione) e (deve corrispondere al valore assegnato per il gruppo di risorse).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Vedere anche

* [Interfaccia della riga di comando di AzureAzure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Collegare uno spazio di archiviazione secondario a un account di Servizi multimedialiAttach a secondary storage to a Media Services account](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
