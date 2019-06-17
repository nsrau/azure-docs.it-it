---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132768"
---
## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, è possibile usare questo comando per rimuovere il gruppo di risorse, l'istanza di Cache Redis di Azure ed eventuali risorse correlate nel gruppo di risorse.

```azurecli
az group delete --name contosoGroup
```