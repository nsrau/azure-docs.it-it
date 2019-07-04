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
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179926"
---
## <a name="create-a-function"></a>Creare una funzione

Il comando seguente crea una funzione attivata tramite HTTP e denominata `MyHttpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Quando viene eseguito il comando, viene visualizzato qualcosa di simile a quanto segue:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
