---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026202"
---
Il fuso orario predefinito usato con le espressioni CRON è Coordinated Universal Time (UTC). Per fare in modo che l'espressione CRON sia basata su un altro fuso orario, creare un'impostazione per l'app per le funzioni denominata `WEBSITE_TIME_ZONE`. 

Il valore di questa impostazione dipende dal sistema operativo e dal piano in cui viene eseguita l'app per le funzioni.

|Sistema operativo |Pianificazione |Valore |
|-|-|-|
| **Windows** |Tutti | Impostare il valore sul nome del fuso orario desiderato come specificato dalla seconda riga di ogni coppia fornita dal comando di Windows `tzutil.exe /L` |
| **Linux** |Premium<br/>Dedicato |Impostare il valore sul nome del fuso orario desiderato, come illustrato nel [database TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` non è attualmente supportato nel piano a consumo Linux.

Ad esempio, l'ora orientale negli Stati Uniti (rappresentata da `Eastern Standard Time` (Windows) o `America/New_York` (Linux)) USA attualmente utc-05:00 durante l'ora solare e utc-04:00 durante l'ora legale. Per attivare un trigger timer alle 10:00 AM Eastern Time ogni giorno, creare un'impostazione app per l'app per le funzioni denominata `WEBSITE_TIME_ZONE` , impostare il valore su `Eastern Standard Time` (Windows) o `America/New_York` (Linux), quindi usare l'espressione NCRONTAB seguente: 

```
"0 0 10 * * *"
``` 

Quando si usa `WEBSITE_TIME_ZONE` il tempo viene regolato per le modifiche dell'ora nel fuso orario specifico, inclusa l'ora legale e le modifiche nell'ora solare.
