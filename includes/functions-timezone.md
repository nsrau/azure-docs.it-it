---
ms.openlocfilehash: dba7a3cc7a68d360fd6e56511b71ae364f624646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569280"
---
Il fuso orario predefinito usato con le espressioni CRON è Coordinated Universal Time (UTC). Per fare in modo che l'espressione CRON sia basata su un altro fuso orario, creare un'impostazione per l'app per le funzioni denominata `WEBSITE_TIME_ZONE`. 

Il valore di questa impostazione dipende dal sistema operativo e dal piano in cui viene eseguita l'app per le funzioni.

|Sistema operativo |Piano |Valore |
|-|-|-|
| **Windows** |Tutti | Impostare il valore sul nome del fuso orario prescelto come illustrato nell'[indice dei fusi orari di Microsoft](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)). |
| **Linux** |Premium<br/>Dedicato |Impostare il valore sul nome del fuso orario desiderato, come illustrato nel [database TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` non è attualmente supportato nel piano a consumo Linux.

Ad esempio, *ora solare fuso orientale* (Windows) o *America/New_York* (Linux) è UTC-05:00. Per attivare il trigger del timer alle 10:00 AM EST ogni giorno, usare l'espressione NCRONTAB seguente che rappresenta il fuso orario UTC:

```
"0 0 15 * * *"
``` 

In alternativa, creare un'impostazione di app per l'app per le funzioni denominata `WEBSITE_TIME_ZONE` , impostare il valore su `Eastern Standard Time` (Windows) o `America/New_York` (Linux), quindi usare l'espressione NCRONTAB seguente: 

```
"0 0 10 * * *"
``` 

Quando si usa `WEBSITE_TIME_ZONE`, l'ora viene regolata per modifiche all'ora nel fuso orario specifico, ad esempio l'ora legale. 
