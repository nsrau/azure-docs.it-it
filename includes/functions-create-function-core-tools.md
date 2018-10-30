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
ms.openlocfilehash: fa1046e8d752e133813924957b439b63fc2acfbd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987978"
---
## <a name="create-a-function"></a>Creare una funzione

Il comando seguente crea una funzione attivata tramite HTTP e denominata `MyHtpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Quando viene eseguito il comando, viene visualizzato qualcosa di simile a quanto segue:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```