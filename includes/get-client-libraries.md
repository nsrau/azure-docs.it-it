---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 9e9057073c8a661e2f3382333abc7ac2778c4ee3
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84680269"
---
### <a name="install-via-composer"></a>Installazione tramite Composer
1. Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il codice seguente:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Scaricare **[composer.phar][composer-phar]** nella radice del progetto.
3. Aprire un prompt dei comandi ed eseguire il comando seguente nella radice del progetto
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
