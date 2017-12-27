---
title: Abilitazione di Acquisizione di Hub eventi di Azure tramite il portale | Microsoft Docs
description: "Abilitare la funzionalità Acquisizione di Hub eventi usando il portale di Azure."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 39fbdba404bda9383c9164dd1ecd9cb23bfb5cd7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Abilitare Acquisizione di Hub eventi usando il portale di Azure

La [funzionalità di acquisizione di Hub eventi][capture-overview] di Azure consente di distribuire automaticamente i dati di streaming di Hub eventi all'[archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) o all'account [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) desiderato.

È possibile configurare Acquisizione al momento della creazione dell'hub eventi usando il [portale di Azure](https://portal.azure.com). È possibile acquisire i dati in un contenitore di [archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/) di Azure o in un account [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Per altre informazioni, vedere la [panoramica della funzionalità di acquisizione di Hub eventi][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Acquisire i dati in un account di archiviazione di Azure  

Quando si crea un hub eventi, è possibile abilitare l'acquisizione facendo clic sul pulsante **Sì** nella schermata del portale **Crea hub eventi**. È quindi possibile specificare un account e un contenitore di archiviazione facendo clic su **Archiviazione di Azure** nella casella **Provider di acquisizione**. Poiché Acquisizione di Hub eventi usa l'autenticazione da servizio a servizio con la risorsa di archiviazione, non è necessario specificare una stringa di connessione di archiviazione. Il selettore risorse seleziona automaticamente l'URI della risorsa per l'account di archiviazione. Se si usa Azure Resource Manager, è necessario specificare questo URI in modo esplicito come stringa.

L'intervallo di tempo predefinito è di 5 minuti. Il valore minimo è 1, quello massimo 15. La finestra **Dimensione** ha un intervallo compreso tra 10 e 500 MB.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Acquisire i dati un account Azure Data Lake Store

Per acquisire i dati in Azure Data Lake Store, si creano un account Data Lake Store e un hub eventi.

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Creare un account Azure Data Lake Store e le cartelle

1. Creare un account Data Lake Store seguendo le istruzioni riportate in [Introduzione ad Azure Data Lake Store con il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Seguire le istruzioni della sezione [Assegnare autorizzazioni a Hub eventi](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) per creare una cartella nell'account Data Lake Store in cui acquisire i dati da Hub eventi e assegnare a Hub eventi le autorizzazioni per scrivere i dati nell'account Data Lake Store.  

### <a name="create-an-event-hub"></a>Creare un hub eventi

1. Si noti che l'hub eventi deve trovarsi nella stessa sottoscrizione di Azure dell'account Azure Data Lake Store appena creato. Creare l'hub eventi facendo clic sul pulsante **Sì** sotto **Acquisisci** nella pagina del portale **Crea hub eventi**. 
2. Nella pagina del portale **Crea hub eventi** selezionare **Azure Data Lake Store** nella casella **Provider di acquisizione**.
3. In **Seleziona Data Lake Store** specificare l'account Data Lake Store creato in precedenza e nel campo **Percorso Data Lake** immettere il percorso della cartella dati creata.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Aggiungere o configurare l'acquisizione in un hub eventi esistente

È possibile configurare l'acquisizione in hub eventi esistenti che si trovano in spazi dei nomi di Hub eventi. Per abilitare l'acquisizione in un hub eventi esistente o per modificarne le impostazioni, fare clic sullo spazio dei nomi per caricare la schermata Panoramica e quindi sull'hub eventi per cui si vuole abilitare l'acquisizione o modificarne l'impostazione. Infine fare clic sull'opzione **Acquisisci** nella parte sinistra della pagina aperta e quindi modificare le impostazioni come illustrato nelle figure seguenti:

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

![][2]

### <a name="azure-data-lake-store"></a>Archivio Azure Data Lake

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sull'acquisizione, vedere la [panoramica della funzionalità di acquisizione di Hub eventi][capture-overview].
- È anche possibile configurare Acquisizione di Hub eventi usando i modelli di Azure Resource Manager. Per altre informazioni, vedere [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Abilitare Acquisizione usando un modello di Azure Resource Manager).
- [Introduzione ad Azure Data Lake Store con il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md