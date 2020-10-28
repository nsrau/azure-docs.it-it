---
title: Elemento StorageBlobSelector dell'interfaccia utente
description: Descrive l'elemento Microsoft. storage. StorageBlobSelector dell'interfaccia utente per portale di Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754533"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Elemento Microsoft. storage. StorageBlobSelector dell'interfaccia utente

Controllo per la selezione di un BLOB da un account di archiviazione di Azure.

## <a name="ui-sample"></a>Esempio di interfaccia utente

All'utente viene visualizzata l'opzione per cercare i BLOB di archiviazione disponibili.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft. storage. StorageBlobSelector-Sfoglia":::

Dopo aver selezionato **Sfoglia** , l'utente può selezionare un account di archiviazione.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft. storage. StorageBlobSelector-Sfoglia":::

L'utente Visualizza i contenitori nell'account di archiviazione e può selezionarne uno.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft. storage. StorageBlobSelector-Sfoglia":::

Dal contenitore, l'utente può selezionare un file.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft. storage. StorageBlobSelector-Sfoglia":::

Il controllo viene aggiornato per visualizzare il nome file selezionato.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft. storage. StorageBlobSelector-Sfoglia":::

## <a name="schema"></a>SCHEMA

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Output di esempio

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Commenti

La proprietà **Constraints. allowedFileExtensions** specifica i tipi di file consentiti.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
