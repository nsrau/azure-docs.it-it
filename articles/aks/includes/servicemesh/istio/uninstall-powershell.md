---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 4bd2af67ed0e570e8d171da678b69d0908627374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593980"
---
Per eliminare i segreti, eseguire il comando seguente:

```powershell
(kubectl get secret --all-namespaces -o json | ConvertFrom-Json).items.metadata |% { if ($_.name -match "istio.") { "Deleting {0}.{1}" -f $_.namespace, $_.name; kubectl delete secret -n $_.namespace $_.name } }
```
