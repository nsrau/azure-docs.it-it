---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201699"
---
### <a name="install-via-composer"></a>Installazione tramite Composer
1. Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il codice seguente:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Scaricare **[composer.phar][composer-phar]** nella radice del progetto.
3. Aprire un prompt dei comandi ed eseguire il comando seguente nella radice del progetto
   
    ```
    php composer.phar install
    ```

In alternativa andare alla [libreria client PHP di Archiviazione di Microsoft Azure][php-sdk-github] su GitHub per clonare il codice sorgente.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
