---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987987"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Il comando seguente avvia l'app della funzione. L'app viene eseguita tramite lo stesso runtime di funzioni di Azure presente in Azure.

```bash
func host start --build
```

L'opzione `--build` è necessaria per compilare i progetti C#. Questa opzione non è necessaria per un progetto JavaScript.

Quando viene avviato l'host delle funzioni, verrà visualizzata una schermata simile alla seguente, la quale è stata troncata per migliorarne la leggibilità:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copiare l'URL della funzione `HttpTrigger` dall'output del runtime e incollarlo nella barra degli indirizzi del browser. Aggiungere la stringa di query `?name=<yourname>` all'URL ed eseguire la richiesta. Di seguito è illustrata la risposta nel browser alla richiesta GET restituita dalla funzione locale:

![Testare in locale nel browser](./media/functions-run-function-test-local/functions-test-local-browser.png)

Ora che è stata eseguita la funzione in locale, è possibile creare l'app della funzione e altre risorse necessarie in Azure.