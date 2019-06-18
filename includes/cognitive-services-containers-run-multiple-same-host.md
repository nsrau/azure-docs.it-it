---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2019
ms.openlocfilehash: 327654ca06a3997855d904414fa4258491ee6c88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180422"
---
### <a name="run-multiple-containers-on-the-same-host"></a>Eseguire più contenitori nello stesso host

Se si intende eseguire più contenitori con porte esposte, assicurarsi di eseguire ogni contenitore con una porta esposta diversa. Eseguire ad esempio il primo contenitore sulla porta 5000 e il secondo sulla porta 5001.

Questo contenitore e un contenitore di Servizi cognitivi diverso sono in esecuzione contemporaneamente sull'HOST. Sono inoltre in esecuzione più contenitori dello stesso contenitore di Servizi cognitivi.
