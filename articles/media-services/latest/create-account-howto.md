---
title: Creare un account di Servizi multimediali di Azure
description: Questa esercitazione illustra i passaggi per la creazione di un account di servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93f5e4d659b94bd79345a5e687de14ab6a5e8ba6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267973"
---
# <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Per avviare le operazioni di crittografia, codifica, analisi, gestione e streaming dei contenuti multimediali in Azure, è necessario creare un account di Servizi multimediali. L'account di Servizi multimediali deve essere associato a uno o più account di archiviazione. Questo articolo descrive i passaggi per la creazione di un nuovo account di servizi multimediali di Azure.

> [!NOTE]
> L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi.

 Per creare un account di servizi multimediali, è possibile usare l'portale di Azure o l'interfaccia della riga di comando. Scegliere la scheda per il metodo che si vuole usare.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portale](#tab/portal/)

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
Il portale di Azure fornisce un modo per creare rapidamente un account di servizi multimediali di Azure. È possibile utilizzare l'account per accedere ai servizi multimediali che consentono di archiviare, crittografare, codificare, gestire e distribuire in streaming contenuti multimediali in Azure.

Attualmente, è possibile utilizzare il [portale di Azure](https://portal.azure.com/) per:

* gestione [degli eventi live](live-events-outputs-concept.md)di servizi multimediali V3 
* visualizzare (non gestire) gli [Asset](assets-concept.md)V3, 
* [ottenere informazioni sull'accesso alle API](./access-api-howto.md). 

Per tutte le altre attività di gestione (ad esempio, [trasformazioni e processi](transforms-jobs-concept.md) e [protezione del contenuto](content-protection-overview.md)), usare l' [API REST](https://aka.ms/ams-v3-rest-ref), l' [interfaccia](https://aka.ms/ams-v3-cli-ref)della riga di comando o uno degli [SDK](media-services-apis-overview.md#sdks)supportati.
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Usare il portale di Azure per creare un account di servizi multimediali

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Fare clic su **+ Crea una risorsa**  >  **Media**  >  **servizi multimediali**.
1. Nella sezione **creare un account di servizi multimediali** immettere i valori necessari.

    | Nome | Description |
    | ---|---|
    |**Account Name** (Nome account)|Immettere il nome del nuovo account di servizi multimediali. Un nome di account di Servizi multimediali deve essere composto solo da lettere minuscole o da numeri senza spazi con una lunghezza compresa tra 3 e 24 caratteri.|
    |**Sottoscrizione**|Se si hanno più sottoscrizioni, selezionarne una dall'elenco delle sottoscrizioni di Azure a cui si ha accesso.|
    |**Gruppo di risorse**|Selezionare la risorsa nuova o esistente. Un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Fare clic [qui](../../azure-resource-manager/management/overview.md#resource-groups) per altre informazioni.|
    |**Posizione**|Selezionare l'area geografica che verrà usata per archiviare i record di supporti e metadati per l'account di servizi multimediali. Questa area verrà usata per elaborare e riprodurre in streaming il contenuto multimediale. Nella casella dell'elenco a discesa vengono visualizzate solo le aree di Servizi multimediali disponibili. |
    |**Storage Account**|Selezionare un account di archiviazione per fornire l'archiviazione BLOB del contenuto multimediale dell'account di servizi multimediali. È possibile scegliere un account di archiviazione esistente nella stessa area geografica dell'account di Servizi multimediali oppure è possibile crearne uno nuovo. Un nuovo account di archiviazione viene creato nella stessa area geografica. Per i nomi degli account di archiviazione vengono seguite le stesse regole dei nomi degli account di Servizi multimediali.<br/><br/>È necessario avere un account di archiviazione **primario** ed è possibile avere un numero qualsiasi di account di archiviazione **secondari** associati all'account di Servizi multimediali. È possibile usare la portale di Azure per aggiungere account di archiviazione secondari. Per altre informazioni, vedere [account di archiviazione di Azure con account di servizi multimediali di Azure](storage-account-concept.md).<br/><br/>L'account di Servizi multimediali e tutti gli account di archiviazione associati devono far parte della stessa sottoscrizione di Azure. È consigliabile usare account di archiviazione nella stessa posizione dell'account di Servizi multimediali per evitare latenza e costi di dati in uscita aggiuntivi.|

1. Selezionare **Aggiungi al dashboard** per visualizzare lo stato della distribuzione di account.
1. Fare clic su **Crea** nella parte inferiore del form.

    Quando viene creato l'account di servizi multimediali, all'account viene aggiunto un endpoint di streaming **predefinito** con stato **interrotto** . Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione [dinamica dei pacchetti](dynamic-packaging-overview.md) e della [crittografia dinamica](content-protection-overview.md), l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato in **esecuzione** . 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Usare l'interfaccia della riga di comando di Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Impostare la sottoscrizione di Azure

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Creare un account di Servizi multimediali

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Vedi anche

* [Interfaccia della riga di comando di Azure](/cli/azure/ams?view=azure-cli-latest)
* [Alleghi una risorsa di archiviazione secondaria a un account di servizi multimediali](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Passaggi successivi

[Eseguire lo streaming di un file](stream-files-dotnet-quickstart.md)
