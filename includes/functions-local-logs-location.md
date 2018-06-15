---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "33814629"
---
Quando l'host di Funzioni viene eseguito in locale, scrive i log nel percorso seguente:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

In Windows `<DefaultTempDirectory>` è il primo valore trovato delle variabili di ambiente TMP, TEMP, USERPROFILE o la directory di Windows.
In MacOS o Linux `<DefaultTempDirectory>` è la variabile di ambiente TMPDIR.

> [!NOTE]
> Quando l'host di Funzioni viene avviato, sovrascrive la struttura di file esistente nella directory.