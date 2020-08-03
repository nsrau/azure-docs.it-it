---
title: Configurare un mapping del set di dati nella condivisione dati di Azure
description: Informazioni su come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione di dati di Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 82de05e7169c5803dd999521f61a33b9dd17b567
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511956"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Come configurare un mapping del set di dati per una condivisione ricevuta in una condivisione dati di Azure

Questo articolo illustra come configurare un mapping del set di dati per una condivisione ricevuta usando la condivisione di dati di Azure. Questa operazione può essere eseguita se è stato accettato un invito alla condivisione dati ma si è scelto di "accettare e configurare in un secondo momento" oppure se i dati sono condivisi sul posto. È possibile configurare un mapping del set di dati se è necessario modificare la destinazione per i dati condivisi con l'utente o se si desidera ricevere dati in un SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Passare a una condivisione di dati ricevuta

Nel servizio Condivisione dati di Azure passare alla condivisione ricevuta e selezionare la scheda **Dettagli** . 

![Mapping del set di dati](./media/dataset-mapping.png "Mapping del set di dati") 

Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **annullare** per annullare il mapping esistente. Selezionare **+ mappa per** scegliere un nuovo archivio di destinazione. 

![Mappa a destinazione](./media/dataset-map-target.png "Mappa a destinazione") 

## <a name="select-a-new-target-store"></a>Selezionare un nuovo archivio di destinazione

Selezionare un tipo di dati di destinazione in cui si desidera che vengano inseriti i dati. Per la condivisione basata su snapshot, tutti i dati già esistenti in tutti gli account di archiviazione con mapping in precedenza non verranno spostati automaticamente nel nuovo archivio di destinazione. Per la condivisione sul posto selezionare un archivio dati nel percorso specificato. Il percorso è il data center di Azure in cui si trova l'archivio dati di origine del provider di dati.

![Account di archiviazione di destinazione](./media/dataset-map-target-sql.png "Archiviazione di destinazione") 

## <a name="select-a-file-format-sql-sources-only"></a>Selezionare un formato di file (solo origini SQL)

Se i dati di origine provengano da un'origine basata su SQL, è possibile scegliere il formato in cui vengono ricevuti. 

![Scegli il formato](./media/sql-file-formats.png "Formati di file SQL")

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).



