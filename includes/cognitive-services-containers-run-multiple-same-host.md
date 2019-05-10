---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 19726330561abfad08aec3c4908c855616c6ee29
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520943"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Esecuzione di più contenitori nello stesso host

Se si prevede di eseguire più contenitori con porte esposte, assicurarsi di eseguire ciascun contenitore con un'altra porta esposta. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

È possibile che questo contenitore e un altro contenitore di servizi cognitivi in esecuzione contemporaneamente nell'HOST oppure è possibile avere più contenitori dello stesso contenitore di servizi cognitivi in esecuzione.
