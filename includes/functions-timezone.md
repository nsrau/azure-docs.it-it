---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165660"
---
Il fuso orario predefinito usato con le espressioni CRON è Coordinated Universal Time (UTC). Per fare in modo che l'espressione CRON sia basata su un altro fuso orario, creare un'impostazione per l'app per le funzioni denominata `WEBSITE_TIME_ZONE`. 

Il valore di questa impostazione dipende dal sistema operativo e dal piano in cui viene eseguita l'app per le funzioni.

|Sistema operativo |Piano |valore |
|-|-|-|
| **Windows** |Tutti | Impostare il valore sul nome del fuso orario desiderato, come illustrato nell'[indice del fuso orario Microsoft] ( https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) . |
| **Linux** |Premium<br/>Dedicato |Impostare il valore sul nome del fuso orario desiderato, come illustrato nel [database TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE`non è attualmente supportato nel piano a consumo Linux.

Ad esempio, *ora solare fuso orientale* (Windows) o *America/New_York* (Linux) è UTC-05:00. Per attivare il trigger del timer alle 10:00 AM EST ogni giorno, usare l'espressione NCRONTAB seguente che rappresenta il fuso orario UTC:

```
"0 0 15 * * *"
``` 

In alternativa, creare un'impostazione di app per l'app per le funzioni denominata `WEBSITE_TIME_ZONE` , impostare il valore su `Eastern Standard Time` (Windows) o `America/New_York` (Linux), quindi usare l'espressione NCRONTAB seguente: 

```
"0 0 10 * * *"
``` 

Quando si usa `WEBSITE_TIME_ZONE`, l'ora viene regolata per modifiche all'ora nel fuso orario specifico, ad esempio l'ora legale. 
