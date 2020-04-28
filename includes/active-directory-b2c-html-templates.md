---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78189022"
---
## <a name="sample-templates"></a>Modelli di esempio
È possibile trovare modelli di esempio per la personalizzazione dell'interfaccia utente qui:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Questo progetto contiene i modelli seguenti:
- [Blu oceano](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Grigio ardesia](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Per utilizzare l'esempio:

1. Clonare il repository nel computer locale. Scegliere una cartella `/ocean_blue` del modello `/slate_gray`o.
1. Caricare tutti i file nella cartella del modello e nella `/assets` cartella nell'archivio BLOB, come descritto nelle sezioni precedenti.
1. Aprire quindi ogni `\*.html` file nella radice di `/ocean_blue` o `/slate_gray`, sostituire tutte le istanze degli URL relativi con gli URL dei file CSS, images e fonts caricati nel passaggio 2. Ad esempio:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    A
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Salvare i `\*.html` file e caricarli nell'archivio BLOB.
1. A questo punto, modificare il criterio, puntando al file HTML, come indicato in precedenza.
1. Se vengono visualizzati i tipi di carattere, le immagini o i file CSS mancanti, controllare i riferimenti nei \*criteri delle estensioni e nei file con estensione HTML.
