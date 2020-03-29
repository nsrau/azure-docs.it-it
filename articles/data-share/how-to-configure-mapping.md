---
title: Configurare un mapping del set di dati nella condivisione dati di AzureConfigure a dataset mapping in Azure Data Share
description: Informazioni su come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione dati di Azure.Learn how to configure a dataset mapping for a received share using Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964244"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Come configurare un mapping del set di dati per una condivisione ricevuta nella condivisione dati di AzureHow to configure a dataset mapping for a received share in Azure Data Share

Questo articolo illustra come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione dati di Azure.This article explains how to configure a dataset mapping for a Received Share using Azure Data Share. È consigliabile eseguire questa operazione se è stato accettato un invito alla condivisione dati, ma si è scelto di "Accettare e configurare in un secondo momento" o se i dati vengono condivisi sul posto. You may want to configure a dataset mapping if you need to change the destination for data being shared with you, or if you want to receive data into a SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Passare a una condivisione dati ricevuta

Nel servizio Condivisione dati di Azure passare alla condivisione ricevuta e selezionare la scheda **Dettagli.In** the Azure Data Share service, navigate to your received share and select the Details tab. 

![Mapping dei set di dati](./media/dataset-mapping.png "Mapping dei set di dati") 

Seleziona la casella accanto al set di dati a cui desideri assegnare una destinazione. Selezionare **Annulla mapping** per annullare il mapping del mapping esistente. Selezionare **Mappa di destinazione** per scegliere un nuovo punto vendita di destinazione. 

![Mappa al target](./media/dataset-map-target.png "Mappa al target") 

## <a name="select-a-new-target-store"></a>Selezionare un nuovo punto vendita di destinazione

Selezionare un tipo di dati di destinazione in cui si desidera ottenere i dati. Per la condivisione basata su snapshot, tutti i dati già esistenti in tutti gli account di archiviazione mappati in precedenza non verranno spostati automaticamente nel nuovo archivio di destinazione. Per la condivisione sul posto, selezionare un archivio dati nel percorso specificato. Il percorso è il data center di Azure in cui si trova l'archivio dati di origine del provider di dati.

![Account di archiviazione di destinazione](./media/dataset-map-target-sql.png "Archiviazione di destinazione") 

## <a name="select-a-file-format-sql-sources-only"></a>Selezionare un formato di file (solo origini SQL)Select a file format (SQL sources only)

Se i dati di origine provengono da un'origine basata su SQL, è possibile scegliere il formato in cui vengono ricevuti. 

![Scegli il formato](./media/sql-file-formats.png "Formati di file SQL")

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).



