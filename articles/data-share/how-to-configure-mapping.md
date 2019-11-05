---
title: Configurare un mapping del set di dati nella condivisione dati di Azure
description: Informazioni su come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione di dati di Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490611"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Come configurare un mapping del set di dati per una condivisione ricevuta in una condivisione dati di Azure

Questo articolo illustra come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione di dati di Azure. Questa operazione deve essere eseguita se è stato accettato un invito alla condivisione dati, ma si è scelto di accettare e configurare in un secondo momento. È possibile configurare un mapping del set di dati se è necessario modificare la destinazione per i dati condivisi con l'utente o se si desidera ricevere dati in un SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Passare a una condivisione di dati ricevuta

Nel servizio Condivisione dati di Azure passare alla condivisione ricevuta e selezionare la scheda **Dettagli** . 

![Mapping del set di dati](./media/dataset-mapping.png "Mapping del set di dati") 

Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **annullare** per annullare il mapping esistente. Selezionare **+ mappa per** scegliere un nuovo archivio di destinazione. 

![Mappa a destinazione](./media/dataset-map-target.png "Mappa a destinazione") 

## <a name="select-a-new-destination-store"></a>Selezionare un nuovo archivio di destinazione

Selezionare un tipo di dati di destinazione in cui si desidera che vengano inseriti i dati. Si noti che tutti i dati già esistenti in qualsiasi account di archiviazione con mapping in precedenza non verranno spostati automaticamente nella nuova destinazione.

![Account di archiviazione di destinazione](./media/dataset-map-target-sql.png "Archiviazione di destinazione") 

## <a name="select-a-file-format-sql-sources-only"></a>Selezionare un formato di file (solo origini SQL)

Se i dati di origine provengano da un'origine basata su SQL, è possibile scegliere il formato in cui vengono ricevuti. 

![Scegli il formato](./media/sql-file-formats.png "Formati di file SQL")

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).



