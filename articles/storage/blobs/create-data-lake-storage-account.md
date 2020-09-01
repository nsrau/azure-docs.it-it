---
title: Creare un account di archiviazione per Azure Data Lake Storage Gen2
description: Informazioni su come creare un account di archiviazione da usare con Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6b63933fc625079bb490942cf3a32232a484fe38
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270164"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Creare un account di archiviazione da usare con Azure Data Lake Storage Gen2

Per usare le funzionalità di Data Lake Storage Gen2, creare un account di archiviazione con uno spazio dei nomi gerarchico.

## <a name="choose-a-storage-account-type"></a>Scegliere un tipo di account di archiviazione

Le funzionalità Data Lake Storage sono supportate nei tipi di account di archiviazione seguenti:

- Utilizzo generico v2
- BlockBlobStorage

Per informazioni su come scegliere tra di essi, vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Creare un account di archiviazione con uno spazio dei nomi gerarchico

Creare un [account per utilizzo generico V2](../common/storage-account-create.md) o un account [BlockBlobStorage](storage-blob-create-account-block-blob.md) con l'impostazione **dello spazio dei nomi gerarchica** abilitata.

Sbloccare le funzionalità di Data Lake Storage quando si crea l'account abilitando l'impostazione **spazio dei nomi gerarchico** nella scheda **Avanzate** della pagina **Crea account di archiviazione** . È necessario abilitare questa impostazione quando si crea l'account. Non è possibile abilitarlo in seguito.

La figura seguente illustra questa impostazione nella pagina **Crea account di archiviazione** .

> [!div class="mx-imgBorder"]
> ![Impostazione dello spazio dei nomi gerarchico](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Se si ha un account di archiviazione esistente che si vuole usare con Data Lake Storage e l'impostazione dello spazio dei nomi gerarchico è disabilitata, è necessario eseguire la migrazione dei dati in un nuovo account di archiviazione in cui è abilitata l'impostazione.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione](../common/storage-account-overview.md)
- [Uso di Azure Data Lake Storage Gen2 per i requisiti di Big Data](data-lake-storage-data-scenarios.md)
- [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)