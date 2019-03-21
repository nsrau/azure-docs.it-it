---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753801"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Esecuzione di più contenitori nello stesso host

Se si prevede di eseguire più contenitori con porte esposte, assicurarsi di eseguire ciascun contenitore con una porta diversa. Ad esempio, eseguire il primo contenitore sulla porta 5000 e il secondo contenitore sulla porta 5001.

Sostituire il `<container-registry>` e `<container-name>` con i valori dei contenitori è utilizzare. Questi non è necessario essere il contenitore stesso. È possibile avere i contenitori di viso e LUIS sono in esecuzione contemporaneamente nell'HOST oppure è possibile avere più contenitori di viso in esecuzione. 

Eseguire il primo contenitore sulla porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Eseguire il secondo contenitore sulla porta 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ogni contenitore successivo deve essere su una porta diversa. 
