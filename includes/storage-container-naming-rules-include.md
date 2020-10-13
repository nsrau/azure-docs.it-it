---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3e5afeffa6f47ff79e31f189abc6d5e26d8f2586
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050471"
---
Ogni BLOB nell'archiviazione di Azure deve risiedere in un contenitore. Il contenitore fa parte del nome del BLOB. Ad esempio, `mycontainer` è il nome del contenitore nei seguenti URI del BLOB di esempio:

```http
https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
```

Il nome di un contenitore deve essere un nome DNS valido, in conformità con le regole di denominazione seguenti:

1. I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).
2. Ciascun carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero: nei nomi dei contenitori non sono consentiti trattini consecutivi.
3. Tutte le lettere in un nome di contenitore deve essere composto solo da minuscole.
4. La lunghezza dei nomi dei contenitori deve essere compresa tra 3 e 63 caratteri.

> [!IMPORTANT]
> Il nome di un contenitore deve sempre essere minuscolo. Se si include una lettera maiuscola nel nome di un contenitore o si violano in altro modo le regole di denominazione del contenitore, è possibile che venga visualizzato un errore 400 (richiesta non valida). 
> 
> 

