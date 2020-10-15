---
title: Configurare un mapping del set di dati nella condivisione dati di Azure
description: Informazioni su come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione di dati di Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88257828"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Come configurare un mapping del set di dati per una condivisione ricevuta in una condivisione dati di Azure

Questo articolo illustra come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione di dati di Azure. Potrebbe essere necessario configurare un mapping del set di dati per specificare un archivio dati di destinazione in cui si desidera ricevere i dati o se è necessario modificare l'archivio dati di destinazione.

## <a name="navigate-to-a-received-data-share"></a>Passare a una condivisione di dati ricevuta

Nel servizio Condivisione dati di Azure passare alla condivisione ricevuta e selezionare la scheda **set** di dati. 

![Mapping del set di dati](./media/dataset-mapping.png "Mapping del set di dati") 

Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **+ mappa per** scegliere un nuovo archivio di destinazione. Selezionare prima **annullare** se è già stato eseguito il mapping del set di dati e si desidera modificare l'archivio dati di destinazione.

![Esegui mapping alla destinazione](./media/dataset-map-target.png "Esegui mapping alla destinazione") 

## <a name="select-a-target-store"></a>Selezionare un archivio di destinazione

Selezionare un tipo di archivio dati di destinazione in cui verranno ospitati i dati. Per la condivisione basata su snapshot, tutti i dati già esistenti in tutti gli account di archiviazione con mapping in precedenza non verranno spostati automaticamente nel nuovo archivio di destinazione. Per la condivisione sul posto selezionare un archivio dati nella località specificata. La località è il data center di Azure in cui si trova l'archivio dati di origine del provider di dati.

![Account di archiviazione di destinazione](./media/dataset-map-target-sql.png "Archiviazione di destinazione") 

## <a name="select-a-file-format-sql-sources-only"></a>Selezionare un formato di file (solo origini SQL)

Se i dati di origine provengano da un'origine basata su SQL e si desidera riceverli come file, è possibile scegliere il formato in cui vengono ricevuti. 

![Scegli il formato](./media/sql-file-formats.png "Formati di file SQL")

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).



