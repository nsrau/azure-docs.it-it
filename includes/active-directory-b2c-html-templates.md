---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189022"
---
## <a name="sample-templates"></a>Modelli di esempio
È possibile trovare modelli di esempio per la personalizzazione dell'interfaccia utente qui:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Questo progetto contiene i seguenti modelli:
- [Ocean Blue](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Grigio ardesia](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Per utilizzare l'esempio:

1. Clonare il repository nel computer locale. Scegliere una `/ocean_blue` cartella `/slate_gray`di modelli o .
1. Caricare tutti i file nella `/assets` cartella dei modelli e nella cartella nell'archivio BLOB come descritto nelle sezioni precedenti.
1. Successivamente, aprire `\*.html` ogni file nella `/ocean_blue` `/slate_gray`radice di o , sostituire tutte le istanze di URL relativi con gli URL dei file css, images e font caricati nel passaggio 2. Ad esempio:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    A
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Salvare `\*.html` i file e caricarli nell'archivio BLOB.
1. Ora modificare il criterio, che punta al file HTML, come accennato in precedenza.
1. Se i tipi di carattere, le immagini o i \*CSS mancanti sono presenti, controllare i riferimenti nei criteri delle estensioni e nei file HTML.
